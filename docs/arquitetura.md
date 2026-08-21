# Arquitetura do projeto

## Visão geral

O projeto é dividido em dois workflows independentes.

### Workflow A — Ingestão

Responsável por construir e atualizar a base RAG.

```text
Documentos
   ↓
Google Drive / entrada manual
   ↓
n8n
   ↓
Extração de texto
   ↓
Chunking
   ↓
Embeddings
   ↓
Supabase + pgvector
```

Cada trecho armazenado deve possuir, idealmente:

- conteúdo;
- embedding;
- nome do documento;
- categoria;
- data de atualização;
- identificador da origem.

### Workflow B — Atendimento

```text
WhatsApp
   ↓
Webhook
   ↓
Normalização
   ↓
Busca vetorial
   ↓
Top K documentos
   ↓
Contexto
   ↓
LLM / AI Agent
   ↓
Resposta
   ↓
WhatsApp
```

## Regra de fallback

Caso a busca não retorne contexto confiável, o agente deve retornar uma resposta controlada, por exemplo:

> Não encontrei essa informação com segurança na base de conhecimento. Vou deixar essa solicitação para confirmação do atendimento humano.

## Evolução em relação a uma automação tradicional

Uma automação tradicional costuma manter grande parte das informações diretamente no prompt.

Neste projeto, o prompt define comportamento e regras, enquanto o conhecimento empresarial fica desacoplado e pesquisável por meio de RAG.

Isso permite:

- atualizar documentos sem reescrever todo o prompt;
- trabalhar com bases maiores;
- recuperar apenas contexto relevante;
- reduzir respostas inventadas;
- manter rastreabilidade da origem das informações.
