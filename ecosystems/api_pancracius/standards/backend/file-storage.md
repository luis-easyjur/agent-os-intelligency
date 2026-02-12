# Padrões de Armazenamento de Arquivos no Fênix

## Visão Geral

O Fênix possui a configuração básica do sistema de arquivos do Laravel (Flysystem) em `config/filesystems.php`, mas **não está utilizando armazenamento de arquivos físicos atualmente**. Este documento serve como referência e guia de padrões para implementação futura de funcionalidades de upload/download de arquivos.

## Status Atual

- ✅ Configuração básica do Laravel Filesystem presente em `config/filesystems.php`
- ❌ Não há uso de `Storage::` no código atual
- ❌ Não há modelos ou serviços específicos para gerenciamento de arquivos
- ❌ O modelo `Documento` armazena apenas metadados (texto, texto_blob) no banco de dados

## Drivers de Armazenamento Disponíveis

A configuração atual suporta os seguintes drivers (quando implementado):

1. **Local**: Armazenamento no sistema de arquivos local (`storage/app`)
2. **Public**: Armazenamento público (`storage/app/public`)
3. **S3**: Armazenamento na nuvem AWS (requer variáveis de ambiente)

A configuração dos drivers está em `config/filesystems.php` e pode ser estendida conforme necessário.

## Modelo de Dados (Para Implementação Futura)

Quando implementar armazenamento de arquivos, criar um modelo `File` ou similar para armazenar metadados:

```php
namespace App\Models;

use App\Models\BaseModel;
use App\Models\Traits\Common\TemEscritorio;

class File extends BaseModel
{
    use TemEscritorio;

    protected $guarded = ['id'];

    protected $casts = [
        'size' => 'decimal:2',
    ];
}
```

### Estrutura sugerida da tabela `files`

```php
Schema::create('tb_files', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('id_empresa');
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('model_id')->nullable();
    $table->string('model_type', 256)->nullable();
    $table->string('filename');
    $table->string('path');
    $table->string('mime_type');
    $table->string('extension', 10);
    $table->decimal('size', 10, 2);
    $table->string('disk')->default('local');
    // NOTA: Esta tabela não usa timestamps padrão
});
```

## Serviço de Arquivos (Para Implementação Futura)

Quando implementar, criar um serviço em `app/Services/Documentos/` ou similar:

```php
namespace App\Services\Documentos;

use App\Models\Documentos\File;
use Illuminate\Support\Facades\Storage;
use Illuminate\Http\UploadedFile;

class FileService
{
    public function store(UploadedFile $file, array $data): File
    {
        $path = Storage::disk('local')->put(
            "documentos/{$data['id_empresa']}",
            $file
        );

        return File::create([
            'id_empresa' => $data['id_empresa'],
            'user_id' => $data['user_id'],
            'filename' => $file->getClientOriginalName(),
            'path' => $path,
            'mime_type' => $file->getMimeType(),
            'extension' => $file->getClientOriginalExtension(),
            'size' => $file->getSize() / 1024,
        ]);
    }
    
    public function download(File $file)
    {
        return Storage::disk($file->disk)->download($file->path, $file->filename);
    }
    
    public function delete(File $file): bool
    {
        Storage::disk($file->disk)->delete($file->path);
        return $file->delete();
    }
}
```

## Padrões de Nomenclatura de Arquivos (Para Implementação Futura)

Quando implementar, os arquivos armazenados devem seguir um padrão de nomenclatura consistente:

1. **Formato Geral**: `{id_empresa}/{model_type}/{model_id}/{timestamp}_{original_filename}`
2. **Exemplo**: `123/App_Models_Documento/456/20260119_123456_documento.pdf`

Isso garante:
- Isolamento de arquivos por escritório
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

### Upload de Arquivos (Exemplo para Implementação)

```php
// Exemplo de upload quando implementado
use App\Services\Documentos\FileService;
use App\Http\Requests\Documentos\UploadFileRequest;

class DocumentoController extends Controller
{
    public function upload(
        UploadFileRequest $request,
        FileService $fileService
    ): JsonResponse {
        $user = User::getCurrent();
        
        $file = $fileService->store(
            $request->file('arquivo'),
            [
                'id_empresa' => $user->id_empresa,
                'user_id' => $user->id,
                'model_id' => $request->input('documento_id'),
                'model_type' => Documento::class,
            ]
        );

        return $this->apiResponse('Arquivo enviado com sucesso.', 200, $file);
    }
}
```

### Download de Arquivos (Exemplo para Implementação)

```php
// Exemplo de download quando implementado
public function download(File $file, FileService $fileService)
{
    $user = User::getCurrent();
    
    // Verificar permissão
    if ($file->id_empresa !== $user->id_empresa) {
        abort(403, 'Acesso negado');
    }

    return $fileService->download($file);
}
```

### Exclusão de Arquivos (Exemplo para Implementação)

Ao excluir arquivos, é importante excluir tanto o registro no banco quanto o arquivo físico:

```php
// Exemplo de exclusão quando implementado
public function delete(File $file, FileService $fileService): JsonResponse
{
    $user = User::getCurrent();
    
    if ($file->id_empresa !== $user->id_empresa) {
        abort(403, 'Acesso negado');
    }

    $fileService->delete($file);

    return $this->apiResponse('Arquivo excluído com sucesso.', 200);
}
```

## Arquivos Temporários (Para Implementação Futura)

Para manipulação de arquivos temporários (uploads em etapas, processamento, etc.):

1. Armazenar em pasta específica: `temp/{id_empresa}/{uuid}`
2. Implementar job para limpar arquivos temporários antigos
3. Mover para localização final após processamento concluído

## Segurança

- **Verificação de Tipo MIME**: Validar tipos MIME permitidos para evitar uploads maliciosos
- **Limite de Tamanho**: Configurar limites de tamanho de arquivo por tipo
- **Scan de Malware**: Considerar integração com serviço de scan para arquivos sensíveis
- **Nomes Seguros**: Sanitizar nomes de arquivos antes de armazenar
- **Controle de Acesso**: Implementar políticas de acesso granulares

## Configuração de Quota (Para Implementação Futura)

Quando implementar, o sistema deve controlar o uso de armazenamento por escritório:

1. Configurar quotas por escritório
2. Verificar quota disponível antes de permitir uploads
3. Implementar alertas para escritórios próximos do limite

## Monitoramento e Logs (Para Implementação Futura)

Quando implementar armazenamento de arquivos:

- Registrar todas as operações de arquivo (upload, download, exclusão) através do sistema de logs do Fênix
- Implementar auditoria para rastreabilidade
- Monitorar uso de armazenamento por escritório

## Nota Importante

**Este documento serve como referência para implementação futura.** O projeto Fênix atualmente não utiliza armazenamento de arquivos físicos. Quando for necessário implementar essa funcionalidade, seguir os padrões e boas práticas descritas neste documento.