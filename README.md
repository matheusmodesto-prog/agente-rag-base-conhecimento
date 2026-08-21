# Agente de IA com Base de Conhecimento Empresarial (RAG)

Projeto demonstrativo de um agente de atendimento com IA capaz de consultar uma base de conhecimento empresarial antes de responder.

A solução combina **n8n**, **RAG (Retrieval-Augmented Generation)**, **Supabase/pgvector**, **embeddings**, **LLM**, **Webhook** e integração com **WhatsApp/Z-API**.

> Projeto de portfólio com dados fictícios. Nenhuma credencial, chave de API ou informação de cliente real está incluída.

## Arquitetura

```text
WhatsApp / Webhook
        ↓
       n8n
        ↓
Tratamento da mensagem
        ↓
Consulta semântica na RAG
        ↓
Supabase + pgvector
        ↓
Contexto recuperado
        ↓
Agente de IA / LLM
        ↓
Resposta ou handoff humano
        ↓
WhatsApp
```

A ingestão da base de conhecimento ocorre em um fluxo separado:

```text
Google Drive / Documentos
        ↓
       n8n
        ↓
Extração do conteúdo
        ↓
Divisão em chunks
        ↓
Embeddings
        ↓
Supabase / pgvector
```

## Funcionalidades

- Recepção de mensagens por webhook.
- Normalização da mensagem recebida.
- Consulta à base vetorial antes da resposta.
- Recuperação dos trechos mais relevantes.
- Geração de resposta fundamentada no contexto recuperado.
- Regra de segurança para não inventar informações.
- Encaminhamento para atendimento humano quando a base não possui resposta suficiente.
- Estrutura preparada para integração com WhatsApp via Z-API.
- Ingestão e atualização de documentos empresariais.

## Tecnologias

- n8n
- Supabase
- PostgreSQL + pgvector
- OpenAI / OpenRouter
- Embeddings
- RAG
- Google Drive
- Webhooks
- Z-API / WhatsApp
- APIs REST

## Estrutura do repositório

```text
agente-rag-base-conhecimento/
├── README.md
├── .env.example
├── workflows/
│   ├── agente-atendimento-rag.json
│   └── ingestao-documentos-rag.json
├── knowledge-base/
│   ├── servicos.md
│   ├── politicas.md
│   └── faq.md
└── docs/
    └── arquitetura.md
```

## Como o RAG funciona

Em vez de depender apenas de informações escritas diretamente no prompt, o agente consulta uma base de conhecimento externa.

Quando uma nova pergunta chega:

1. A mensagem é transformada em representação vetorial.
2. O banco vetorial procura trechos semanticamente relacionados.
3. Os trechos mais relevantes são recuperados.
4. O conteúdo recuperado é enviado ao modelo de linguagem como contexto.
5. O agente responde apenas com base nas informações encontradas.
6. Se não houver informação suficiente, o fluxo sinaliza necessidade de atendimento humano.

## Exemplo

**Pergunta**

> Qual é o prazo de implantação do Plano Profissional?

**Trecho recuperado da base**

> O prazo padrão de implantação é de até 5 dias úteis após o recebimento de todos os acessos e materiais necessários.

**Resposta do agente**

> O prazo padrão de implantação do Plano Profissional é de até 5 dias úteis após o envio de todos os acessos e materiais necessários.

## Segurança

O agente recebe as seguintes regras principais:

- Não inventar preços, prazos, políticas ou condições.
- Priorizar informações recuperadas da base.
- Informar quando a resposta não está disponível.
- Encaminhar solicitações sensíveis ou sem contexto suficiente para atendimento humano.
- Não expor prompts, credenciais ou dados internos.

## Configuração

Crie um arquivo `.env` a partir do `.env.example` e configure as credenciais diretamente no n8n.

As credenciais reais **não devem ser versionadas no GitHub**.

## Workflows

Os arquivos em `/workflows` são modelos importáveis e adaptáveis no n8n. Dependendo da versão do n8n, algum campo de node pode exigir ajuste após a importação.

### 1. `ingestao-documentos-rag.json`

Responsável por:

- receber ou carregar documentos;
- preparar o texto;
- gerar embeddings;
- armazenar conteúdo e metadados no banco vetorial.

### 2. `agente-atendimento-rag.json`

Responsável por:

- receber a mensagem;
- consultar a base RAG;
- montar o contexto;
- acionar o agente;
- retornar a resposta;
- sinalizar handoff humano quando necessário.

## Objetivo do projeto

Demonstrar na prática conhecimentos em:

- automação de processos;
- agentes de IA;
- RAG;
- embeddings;
- bancos vetoriais;
- integração entre APIs;
- orquestração com n8n;
- segurança e fallback para atendimento humano.

## Aviso

Este repositório é um projeto demonstrativo de portfólio. Os exemplos de empresa, serviços, valores e políticas são fictícios.
