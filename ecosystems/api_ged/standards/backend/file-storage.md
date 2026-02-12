# Padrões de Armazenamento de Arquivos no APP-GED

## Visão Geral

O APP-GED é um microsserviço especializado no gerenciamento e armazenamento de arquivos, com suporte para múltiplos sistemas de armazenamento. Este documento descreve os padrões e melhores práticas para implementação e uso do sistema de arquivos no projeto.

## Drivers de Armazenamento Suportados

O APP-GED utiliza o sistema de arquivos do Laravel (Flysystem) e suporta os seguintes drivers:

1. **Local**: Armazenamento no sistema de arquivos local
2. **Amazon S3**: Armazenamento na nuvem da AWS
3. **Azure Storage**: Armazenamento na nuvem da Microsoft
4. **Google Cloud Storage**: Armazenamento na nuvem do Google

A configuração dos drivers é feita no arquivo `config/filesystems.php`.

## Modelo de Dados

O armazenamento de metadados dos arquivos é feito através do modelo `File`:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable as InterfaceAuditable;
use OwenIt\Auditing\Auditable;

class File extends Model implements InterfaceAuditable
{
    use Auditable;

    protected $guarded = ['id', 'created_at', 'updated_at'];
}
```

### Estrutura da tabela `files`

```php
Schema::create('files', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('company_id');
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('model_id');
    $table->string('model_type', 256);
    $table->string('db_name', 256);
    $table->string('filename');
    $table->string('url');
    $table->string('mime_type');
    $table->string('extension', 10);
    $table->double('size', 8, 2);
    $table->timestamps();
});
```

## Serviço de Arquivos

A lógica de negócio relacionada a arquivos deve ser implementada no `FileService`:

```php
namespace App\Services;

use App\Models\File;
use Illuminate\Support\Facades\Storage;

class FileService
{
    public function store($file, $data)
    {
        // Lógica de armazenamento
    }
    
    public function download($fileId)
    {
        // Lógica de download
    }
    
    // Outros métodos
}
```

## Padrões de Nomenclatura de Arquivos

Os arquivos armazenados devem seguir um padrão de nomenclatura consistente:

1. **Formato Geral**: `{company_id}/{model_type}/{model_id}/{timestamp}_{original_filename}`
2. **Exemplo**: `123/App_Models_Invoice/456/20260119_123456_invoice.pdf`

Isso garante:
- Isolamento de arquivos por empresa
- Agrupamento por tipo de modelo
- Agrupamento por ID do modelo
- Evita colisões de nomes de arquivos
- Preserva o nome original do arquivo

## Boas Práticas

### Armazenamento

- **Validação de Entrada**: Sempre validar arquivos antes de armazenar (tipo MIME, tamanho, etc.)
- **Permissões**: Verificar permissões do usuário antes de permitir upload/download
- **Isolamento de Empresa**: Garantir que arquivos só possam ser acessados pela empresa correta
- **Storage Facades**: Usar as facades do Laravel para operações de armazenamento (`Storage::put()`, `Storage::get()`, etc.)
- **Visibilidade**: Configurar corretamente a visibilidade dos arquivos (`public` ou `private`)

### Upload de Arquivos

```php
// Exemplo de upload
$path = Storage::disk('s3')->put(
    "company/{$companyId}/files",
    $request->file('file')
);

$file = File::create([
    'company_id' => $companyId,
    'user_id' => auth()->id(),
    'model_id' => $modelId,
    'model_type' => $modelType,
    'db_name' => $dbName,
    'filename' => $request->file('file')->getClientOriginalName(),
    'url' => $path,
    'mime_type' => $request->file('file')->getMimeType(),
    'extension' => $request->file('file')->getClientOriginalExtension(),
    'size' => $request->file('file')->getSize() / 1024, // KB
]);
```

### Download de Arquivos

```php
// Exemplo de download
$file = File::findOrFail($fileId);

// Verificar permissão
if ($file->company_id !== auth()->user()->company_id) {
    abort(403);
}

return Storage::disk('s3')->download($file->url, $file->filename);
```

### Exclusão de Arquivos

Ao excluir arquivos, é importante excluir tanto o registro no banco quanto o arquivo físico:

```php
// Exemplo de exclusão
$file = File::findOrFail($fileId);

// Excluir arquivo físico
Storage::disk('s3')->delete($file->url);

// Excluir registro
$file->delete();
```

## Arquivos Temporários

Para manipulação de arquivos temporários (uploads em etapas, processamento, etc.):

1. Armazenar em pasta específica: `temp/{company_id}/{uuid}`
2. Implementar job para limpar arquivos temporários antigos
3. Mover para localização final após processamento concluído

## Segurança

- **Verificação de Tipo MIME**: Validar tipos MIME permitidos para evitar uploads maliciosos
- **Limite de Tamanho**: Configurar limites de tamanho de arquivo por tipo
- **Scan de Malware**: Considerar integração com serviço de scan para arquivos sensíveis
- **Nomes Seguros**: Sanitizar nomes de arquivos antes de armazenar
- **Controle de Acesso**: Implementar políticas de acesso granulares

## Configuração de Quota

O sistema deve controlar o uso de armazenamento por empresa:

1. Configurar quotas por empresa
2. Verificar quota disponível antes de permitir uploads
3. Implementar alertas para empresas próximas do limite

## Monitoramento e Logs

- Registrar todas as operações de arquivo (upload, download, exclusão)
- Implementar auditoria para rastreabilidade
- Monitorar uso de armazenamento por empresa