# Missão do Produto

## Pitch
Fênix é uma plataforma de microserviços Laravel que complementa o sistema principal EasyJur (APP), fornecendo APIs robustas e rotinas especializadas para escritórios de advocacia, permitindo que o sistema principal aproveite recursos avançados do Laravel enquanto mantém sua arquitetura PHP pura.

## Usuários

### Clientes Principais
- **Advogados Autônomos**: Advogados independentes que utilizam o sistema EasyJur e se beneficiam de funcionalidades avançadas fornecidas pelo Fênix através de APIs, como gestão de assinaturas, modelos de documentos e integrações com serviços externos.
- **Pequenos e Médios Escritórios de Advocacia**: Escritórios que dependem do sistema EasyJur para gestão jurídica e utilizam o Fênix para funcionalidades especializadas como gestão de assinaturas de produtos IA, pacotes de petições, sincronização financeira e integrações com Hubspot e Asaas.
- **Grandes Escritórios de Advocacia**: Escritórios de grande porte que requerem soluções escaláveis e robustas fornecidas pelo Fênix, incluindo processamento de rotinas em background, análise de documentos com IA, captura automática de publicações e sincronização de dados com sistemas externos.
- **Departamentos Jurídicos Corporativos**: Equipes jurídicas internas que utilizam o EasyJur e se beneficiam das integrações e automações fornecidas pelo Fênix para gestão de contratos, receitas e notas fiscais.

### Personas de Usuário

**Advogado Autônomo** (30-55 anos)
- **Papel:** Advogado independente utilizando o sistema EasyJur
- **Contexto:** Utiliza o sistema principal (APP) para gestão diária e consome APIs do Fênix para funcionalidades avançadas como modelos de petições, gestão de assinaturas de produtos IA e sincronização de dados
- **Pontos de Dor:** Necessidade de funcionalidades robustas que o sistema principal não oferece nativamente, integração com serviços externos, processamento de rotinas complexas
- **Objetivos:** Acessar funcionalidades avançadas através de APIs, automatizar tarefas através de rotinas do Fênix, integrar com serviços externos (Hubspot, Asaas, Google)

**Sócio de Escritório** (35-60 anos)
- **Papel:** Sócio ou sócio-gerente em escritório que utiliza EasyJur
- **Contexto:** Gerencia escritório que depende do sistema principal e precisa de funcionalidades especializadas fornecidas pelo Fênix, como gestão de assinaturas, sincronização financeira e análise de documentos com IA
- **Pontos de Dor:** Necessidade de integrações robustas com sistemas externos, processamento de grandes volumes de dados, automação de rotinas complexas
- **Objetivos:** Integrar escritório com sistemas externos (CRM, financeiro), automatizar rotinas de processamento, gerenciar assinaturas e pacotes de produtos IA

**Desenvolvedor do Sistema EasyJur** (25-45 anos)
- **Papel:** Desenvolvedor que mantém e estende o sistema principal (APP)
- **Contexto:** Trabalha com PHP puro no sistema principal e consome APIs do Fênix para funcionalidades que requerem recursos do Laravel (ORM, Jobs, Events, etc)
- **Pontos de Dor:** Limitações do PHP puro para funcionalidades complexas, necessidade de recursos avançados do Laravel sem migrar o sistema principal
- **Objetivos:** Aproveitar recursos do Laravel através de APIs, delegar processamento complexo para o Fênix, manter arquitetura do sistema principal intacta

## O Problema

### Limitações Arquiteturais do Sistema Principal
O sistema EasyJur (APP) é construído em PHP puro e possui uma arquitetura legada monolítica. Embora funcional, ele não possui acesso nativo a recursos avançados de frameworks modernos como Laravel, incluindo:
- **ORM Avançado**: Dificuldade em gerenciar relacionamentos complexos e queries otimizadas
- **Processamento Assíncrono**: Falta de sistema robusto de filas e jobs para processamento em background
- **Integrações Complexas**: Dificuldade em integrar com múltiplos serviços externos de forma escalável
- **Automação de Rotinas**: Necessidade de rotinas especializadas para processamento de documentos, análise com IA e captura de dados
- **Gestão de Assinaturas**: Sistema complexo de assinaturas, pacotes e consumo que requer recursos avançados

Essas limitações resultam em:
- **Código duplicado**: Lógica complexa repetida no sistema principal
- **Performance limitada**: Processamento síncrono causando lentidão em operações complexas
- **Manutenção difícil**: Código complexo misturado com lógica de negócio principal
- **Escalabilidade reduzida**: Dificuldade em escalar funcionalidades específicas

**Nossa Solução:** Fênix fornece uma camada de microserviços Laravel que expõe APIs RESTful robustas e rotinas especializadas, permitindo que o sistema principal (APP) aproveite recursos avançados do Laravel sem necessidade de migração arquitetural. O Fênix atua como um complemento especializado, processando operações complexas e fornecendo funcionalidades avançadas através de APIs bem definidas.

## Diferenciais

### Arquitetura de Microserviços Especializada
Diferente de tentar migrar o sistema principal ou criar funcionalidades complexas em PHP puro, o Fênix fornece uma arquitetura de microserviços dedicada que aproveita todos os recursos do Laravel. Isso resulta em código mais limpo, manutenção facilitada e capacidade de escalar funcionalidades específicas independentemente.

### Processamento Assíncrono Robusto
Diferente de processamento síncrono que bloqueia requisições, o Fênix utiliza Laravel Queue para processar rotinas complexas em background, incluindo análise de documentos com IA, captura de publicações, sincronização com sistemas externos e processamento de workflows. Isso resulta em respostas mais rápidas e capacidade de processar grandes volumes de dados.

### Integrações Nativas com Serviços Externos
Diferente de integrações manuais e frágeis, o Fênix fornece integrações robustas e testadas com múltiplos serviços externos (Hubspot, Asaas, Google, Discord, Elasticsearch, Gemini AI), abstraindo complexidade e fornecendo APIs consistentes para o sistema principal. Isso resulta em integrações mais confiáveis e fáceis de manter.

### Sistema de Assinaturas e Pacotes Completo
Diferente de sistemas simples de assinatura básicos, o Fênix fornece um sistema completo de gestão de assinaturas, pacotes, consumo e termos de aceite, com rastreamento de timeline e sincronização com produtos. Isso resulta em gestão profissional de produtos e assinaturas.

## Recursos Principais

### Recursos Essenciais
- **APIs de Autenticação**: Sistema de autenticação via tokens (Sanctum) e login via Google OAuth, fornecendo segurança e flexibilidade para o sistema principal
- **Gestão de Assinaturas e Pacotes**: Sistema completo de gestão de assinaturas de produtos IA, pacotes de petições, consumo de pacotes e aceite de termos, com rastreamento de timeline e sincronização
- **Modelos de Documentos e Petições**: APIs para busca, categorização e gerenciamento de modelos de documentos e petições, incluindo preview e incremento de ranking

### Recursos de Integração
- **Integração com Hubspot**: Sincronização bidirecional de contratos, gestão de eventos via webhooks e cálculo de Health Score para escritórios
- **Integração com Asaas**: Gestão de notas fiscais, cancelamento e sincronização de dados financeiros
- **Integração com Google**: Autenticação OAuth e acesso a serviços Google
- **Integração com Discord**: Sistema de comandos e notificações via Discord para monitoramento de rotinas
- **Integração com Gemini AI**: Análise de documentos jurídicos, extração de informações e processamento inteligente
- **Integração com Elasticsearch**: Busca avançada e indexação de documentos e dados

### Recursos Avançados
- **Rotinas de Processamento em Background**: Sistema robusto de rotinas para captura de publicações, análise de documentos com IA, processamento de workflows e sincronização de dados
- **Gestão de Agenda e Workflows**: APIs para gestão de agendas, workflows com deslocamento de datas e configuração de feriados
- **Gestão Financeira**: APIs para gestão de contratos, receitas, notas fiscais e sincronização com sistemas externos
- **Sistema de Documentos**: Insert prévio de documentos, preview de modelos e gestão de tipos de documentos
- **Gestão de Usuários e Grupos**: APIs para autocomplete, paginação e gestão de usuários e grupos
- **Sistema de Feedbacks**: Coleta e processamento de feedbacks dos usuários
- **Sistema de Busca (AppSearch)**: Busca avançada com Elasticsearch para documentos e dados jurídicos
