# Agente de IA com Base de Conhecimento Empresarial (RAG)

Agente de IA desenvolvido para consultar uma **base de conhecimento empresarial** antes de responder, utilizando **RAG (Retrieval-Augmented Generation)**, embeddings e busca vetorial.

O projeto separa o conhecimento da empresa das instruções do agente: os documentos ficam armazenados em uma base vetorial e apenas os trechos relevantes são recuperados a cada pergunta.

> Projeto de portfólio com dados fictícios. Nenhuma credencial, chave de API ou informação de cliente real está incluída.

## 🎯 Objetivo

Demonstrar como um agente pode responder perguntas com base em documentos empresariais sem depender apenas do conteúdo fixo de um prompt.

A solução foi estruturada para:

- ingerir documentos de conhecimento;
- dividir conteúdo em chunks;
- gerar embeddings;
- armazenar vetores no Supabase/pgvector;
- transformar perguntas em embeddings;
- recuperar os trechos semanticamente mais próximos;
- enviar o contexto recuperado ao LLM;
- responder apenas com base no conhecimento encontrado;
- encaminhar casos sem contexto suficiente para atendimento humano.

## 🧩 Arquitetura

O projeto é dividido em dois fluxos independentes.

### 1. Ingestão da base de conhecimento

```text
Google Drive / Documentos
          ↓
         n8n
          ↓
Extração e preparação do texto
          ↓
Divisão em chunks
          ↓
Geração de embeddings
          ↓
Supabase + pgvector
```

### 2. Atendimento com RAG

```text
WhatsApp / Webhook
        ↓
       n8n
        ↓
Normalização da pergunta
        ↓
Embedding da pergunta
        ↓
Busca vetorial no pgvector
        ↓
Trechos relevantes
        ↓
Contexto para o LLM
        ↓
Resposta fundamentada
        ↓
Resposta ou handoff humano
```

## 🔎 Como o RAG funciona

RAG combina **recuperação de informação** com **geração de linguagem**.

Neste projeto, o processo acontece assim:

1. Os documentos são divididos em trechos menores.
2. Cada trecho é convertido em um vetor numérico por um modelo de embeddings.
3. Os vetores são armazenados no PostgreSQL com a extensão `pgvector`.
4. Quando uma pergunta chega, ela também é convertida em embedding.
5. O banco calcula similaridade entre o vetor da pergunta e os vetores dos documentos.
6. Os trechos mais relevantes são recuperados.
7. Esses trechos são enviados ao LLM como contexto.
8. O agente gera a resposta com base no conteúdo recuperado.
9. Se a base não fornecer contexto suficiente, o fluxo sinaliza handoff humano.

Essa arquitetura permite atualizar o conhecimento sem reescrever o prompt principal do agente.

## ⚙️ Funcionalidades

- recebimento de perguntas via webhook;
- normalização da mensagem;
- ingestão de documentos empresariais;
- chunking de conteúdo;
- geração de embeddings;
- armazenamento vetorial;
- busca semântica por similaridade;
- recuperação dos trechos mais relevantes;
- geração de respostas contextualizadas;
- fallback controlado quando não há informação suficiente;
- estrutura preparada para integração com WhatsApp.

## 🛠️ Tecnologias

- **n8n** — orquestração dos workflows;
- **Supabase** — infraestrutura de dados;
- **PostgreSQL + pgvector** — armazenamento e busca vetorial;
- **OpenAI** — geração de embeddings;
- **OpenRouter / LLM** — geração das respostas;
- **Google Drive** — origem de documentos;
- **Webhooks e APIs REST** — integração entre serviços;
- **Z-API / WhatsApp** — canal de atendimento previsto na arquitetura.

## 📁 Estrutura do repositório

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
    ├── arquitetura.md
    └── supabase-pgvector.sql
```

## 🗂️ Base de conhecimento de exemplo

A pasta `knowledge-base/` contém documentos fictícios para demonstrar o funcionamento do projeto:

- `servicos.md` — catálogo de serviços;
- `politicas.md` — regras e políticas;
- `faq.md` — perguntas frequentes.

## 💬 Exemplo de consulta

**Pergunta**

> Qual é o prazo de implantação do Plano Profissional?

**Conhecimento recuperado**

> O prazo padrão de implantação é de até 5 dias úteis após o recebimento de todos os acessos e materiais necessários.

**Resposta esperada**

> O prazo padrão de implantação do Plano Profissional é de até 5 dias úteis após o envio de todos os acessos e materiais necessários.

## 🧠 Decisões técnicas

### Conhecimento fora do prompt

O prompt define comportamento e regras. O conteúdo empresarial fica desacoplado em documentos, permitindo atualização independente.

### Busca semântica

A recuperação não depende de correspondência exata de palavras. O uso de embeddings permite localizar trechos semanticamente relacionados à pergunta.

### Resposta controlada

O agente recebe instruções para não inventar preços, prazos, políticas ou condições que não estejam presentes no contexto recuperado.

### Fallback humano

Quando o contexto é insuficiente, o fluxo evita completar lacunas por conta própria e encaminha a solicitação para confirmação humana.

## ▶️ Como utilizar

1. Crie um projeto no Supabase.
2. Execute `docs/supabase-pgvector.sql` para preparar a estrutura vetorial.
3. Crie um arquivo `.env` a partir de `.env.example`.
4. Configure as credenciais diretamente no n8n.
5. Importe `workflows/ingestao-documentos-rag.json`.
6. Importe `workflows/agente-atendimento-rag.json`.
7. Adapte a origem dos documentos e o canal de atendimento ao seu ambiente.
8. Teste o fluxo com dados fictícios antes de uso real.

> Dependendo da versão do n8n, algum campo de node pode exigir ajuste após a importação.

## 🔐 Segurança

- nenhuma credencial real está versionada;
- chaves são referenciadas por variáveis de ambiente;
- os documentos de exemplo são fictícios;
- o agente é orientado a não inventar informações ausentes;
- solicitações sem contexto suficiente podem ser direcionadas para atendimento humano.

## 💡 Competências demonstradas

Este projeto apresenta conhecimentos práticos em:

- RAG;
- embeddings;
- bancos vetoriais;
- PostgreSQL e pgvector;
- agentes de IA;
- engenharia de prompts;
- automação com n8n;
- integração entre APIs;
- tratamento de documentos;
- desenho de fallback e segurança para LLMs.

## 👤 Autor

**Matheus Modesto**  
IA aplicada • Automação de Processos • Agentes de IA
