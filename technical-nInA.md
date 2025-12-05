# nInA — Documentação Técnica
Agente de IA do Sistema FIEC

---

## 🏗️ Arquitetura do Sistema

### Visão Geral
```
┌─────────────────┐
│  Google Chat    │  ← Interface do Usuário
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   N8N Workflow  │  ← Orquestração & Lógica
└────────┬────────┘
         │
         ├──────────────┐
         ▼              ▼
┌──────────────┐  ┌─────────────┐
│   OpenAI     │  │  Vector DB  │
│   API        │  │  (Embeddings)│
└──────────────┘  └─────────────┘
```

### Componentes Principais

| Componente | Tecnologia | Função |
|------------|------------|--------|
| **Interface** | Google Chat API | Recebe perguntas e envia respostas |
| **Orquestração** | N8N Community | Gerencia fluxo de dados e lógica de negócio |
| **IA Generativa** | OpenAI (GPT-4) | Processa linguagem natural e gera respostas |
| **Base de Conhecimento** | Vector Database | Armazena embeddings dos documentos internos |
| **Deploy** | Docker Compose + GitLab CI/CD | Containerização e automação |

---

## ⚙️ Integrações

### Google Chat  
**Função:** Canal principal de interação com usuários.  
**Autenticação:** Service Account com permissões de bot.  
**Webhook:** Recebe eventos de mensagens via HTTP POST.

### OpenAI API
**Função:** Motor de IA generativa para processamento de linguagem natural.  
**Modelo:** GPT-4 (ou GPT-3.5-turbo para otimização de custos).  
**Segurança:** API Key armazenada como secret no GitLab CI/CD.  
**Rate Limiting:** Configurado para evitar excesso de chamadas.

### N8N Community  
**Função:** Orquestra workflows e faz parte da infraestrutura de deploy.  
**Deploy:** Docker Compose com volumes persistentes.  
**Workflows:** Automação de recepção, processamento e resposta.

### Vector Database
**Função:** Armazenamento e busca semântica de documentos.  
**Tecnologia:** Pinecone / ChromaDB / Qdrant (a definir).  
**Embeddings:** Gerados via OpenAI Embeddings API.

---

## 🔧 Configuração e Setup

### Pré-requisitos
- Docker & Docker Compose (versão 20.10+)
- GitLab Runner (para CI/CD)
- Acesso às credenciais:
  - Google Service Account (JSON)
  - OpenAI API Key
  - N8N Webhook URL

### Variáveis de Ambiente

| Variável | Descrição | Exemplo |
|----------|-----------|---------|
| `OPENAI_API_KEY` | Chave de API da OpenAI | `sk-proj-...` |
| `GOOGLE_SERVICE_ACCOUNT` | JSON do Service Account | `{"type": "service_account",...}` |
| `N8N_WEBHOOK_URL` | URL do webhook do N8N | `https://n8n.fiec.com.br/webhook/...` |
| `VECTOR_DB_URL` | Endpoint do banco vetorial | `https://vectordb.fiec.com.br` |
| `ENVIRONMENT` | Ambiente de execução | `production` / `staging` |

### Instalação Local

```bash
# Clone o repositório
git clone https://gitlab.com/fiec/nina-agent.git
cd nina-agent

# Configure as variáveis de ambiente
cp .env.example .env
# Edite o .env com suas credenciais

# Suba os containers
docker compose up -d

# Verifique os logs
docker compose logs -f
```

---

## 🚀 Deploy e CI/CD

### Pipeline GitLab

```yaml
stages:
  - validate
  - build
  - deploy

validate:
  stage: validate
  script:
    - docker compose config

build:
  stage: build
  script:
    - docker compose build
  only:
    - main
    - develop

deploy:
  stage: deploy
  script:
    - docker compose up -d
  environment:
    name: production
  only:
    - main
```

### Processo de Deploy
1. **Push para branch `main`** → Trigger automático no GitLab CI
2. **Validação** → `docker compose config` verifica sintaxe
3. **Build** → Imagens Docker são construídas
4. **Deploy** → Containers são atualizados sem downtime
5. **Health Check** → Verifica se serviços estão respondendo

### Rollback
```bash
# Reverter para versão anterior
git revert HEAD
git push origin main

# Ou via GitLab CI (manual trigger na versão anterior)
```

---

## 🔄 Fluxo de Trabalho (N8N)

### 1. Recepção de Mensagem (Google Chat)
```
Trigger: Webhook do Google Chat
↓
Extrai: userId, userName, message, spaceId
↓
Valida: Mensagem mencionou @nInA?
```

### 2. Processamento
```
Busca Semântica: Vector DB → Top 3 documentos relevantes
↓
Monta Contexto: Documentos + Prompt System
↓
Chama OpenAI: Gera resposta contextualizada
```

### 3. Resposta
```
Formata: Markdown + Menção ao usuário
↓
Envia: Google Chat API
↓
Log: Armazena interação (analytics)
```

---

## 📊 Monitoramento e Logs

### Logs Disponíveis

| Tipo | Localização | Retenção |
|------|-------------|----------|
| **N8N Workflow** | `/var/lib/n8n/logs/` | 30 dias |
| **Docker Containers** | `docker compose logs` | 7 dias |
| **OpenAI API** | Dashboard OpenAI | 30 dias |
| **Google Chat** | Google Admin Console | 90 dias |

### Métricas Importantes
- **Tempo de resposta médio** (target: < 3s)
- **Taxa de sucesso** (target: > 95%)
- **Custo por requisição** (OpenAI tokens)
- **Mensagens processadas por dia**

### Comandos Úteis
```bash
# Ver logs em tempo real
docker compose logs -f nina-n8n

# Verificar uso de recursos
docker stats

# Reiniciar serviço específico
docker compose restart nina-n8n

# Backup do banco vetorial
docker compose exec vectordb backup
```

---

## 🧪 Testes

### Testes Unitários
```bash
# N8N workflows (via CLI)
n8n execute --id=<workflow-id> --data=test-input.json
```

### Testes de Integração
```bash
# Teste completo: Google Chat → nInA → Resposta
curl -X POST $N8N_WEBHOOK_URL \
  -H "Content-Type: application/json" \
  -d @test-message.json
```

### Casos de Teste

| Caso | Input | Esperado |
|------|-------|----------|
| Pergunta válida | "Quais são os princípios de qualidade?" | Resposta com base na Política de Qualidade |
| Pergunta fora do escopo | "Qual a previsão do tempo?" | Mensagem de orientação |
| Menção incorreta | Mensagem sem @nInA | Sem resposta |

---

## 🛠️ Troubleshooting

### Problemas Comuns

| Problema | Causa Provável | Solução |
|----------|----------------|---------|
| nInA não responde | Webhook do N8N offline | Verificar `docker compose ps` e reiniciar |
| Resposta muito lenta | OpenAI rate limit | Verificar dashboard OpenAI, aguardar ou ajustar tier |
| Resposta incorreta | Documentos desatualizados no Vector DB | Re-indexar documentos |
| Erro de autenticação | Google Service Account expirado | Renovar credenciais no Google Cloud Console |

### Debug Mode
```bash
# Ativar logs verbosos no N8N
export N8N_LOG_LEVEL=debug
docker compose restart nina-n8n

# Ver requisições OpenAI
export OPENAI_LOG_LEVEL=debug
```

---

## 🔐 Segurança

### Boas Práticas Implementadas
- ✅ API Keys armazenadas como secrets (não commitadas)
- ✅ Service Account com princípio de menor privilégio
- ✅ Comunicação HTTPS entre serviços
- ✅ Rate limiting na OpenAI API
- ✅ Logs não contêm dados sensíveis

### Checklist de Segurança
- [ ] Rotacionar API Keys a cada 90 dias
- [ ] Revisar permissões do Service Account mensalmente
- [ ] Atualizar imagens Docker (security patches)
- [ ] Monitorar uso anômalo da OpenAI API

---

## 📚 Atualização da Base de Conhecimento

### Como Adicionar Novos Documentos

1. **Preparar o documento**
   - Formato: PDF, DOCX ou TXT
   - Verificar qualidade do texto (OCR se necessário)

2. **Gerar embeddings**
   ```bash
   python scripts/generate_embeddings.py \
     --input /path/to/document.pdf \
     --output embeddings/document.json
   ```

3. **Indexar no Vector DB**
   ```bash
   python scripts/index_to_vectordb.py \
     --embeddings embeddings/document.json
   ```

4. **Testar**
   - Fazer perguntas relacionadas ao novo documento
   - Verificar se a nInA retorna informações corretas

### Atualização de Documentos Existentes
```bash
# Re-indexar documento atualizado
python scripts/reindex_document.py \
  --doc-id "politica-qualidade-v2" \
  --input /path/to/updated.pdf
```

---

## 📖 Referências e Links Úteis

| Recurso | Link |
|---------|------|
| **OpenAI API Docs** | https://platform.openai.com/docs |
| **N8N Documentation** | https://docs.n8n.io |
| **Google Chat API** | https://developers.google.com/chat |
| **Docker Compose** | https://docs.docker.com/compose |
| **GitLab CI/CD** | https://docs.gitlab.com/ee/ci |

---

## 🤝 Contribuindo

### Para Desenvolvedores
1. Faça fork do repositório
2. Crie uma branch: `git checkout -b feature/nova-funcionalidade`
3. Commit suas mudanças: `git commit -m 'Adiciona nova funcionalidade'`
4. Push para a branch: `git push origin feature/nova-funcionalidade`
5. Abra um Merge Request

### Padrões de Código
- **Python:** PEP 8
- **JavaScript:** ESLint (Airbnb style)
- **Commits:** Conventional Commits

---

## 📝 Changelog

### v1.0.0 (MVP) - Dezembro 2025
- ✅ Integração com Google Chat
- ✅ Processamento via OpenAI GPT-4
- ✅ Base de conhecimento: Governança de Dados
- ✅ Deploy automatizado via GitLab CI/CD

### Próximas Versões
- 🔜 v1.1.0: Expansão para novos domínios (RH, Financeiro)
- 🔜 v1.2.0: Dashboard de analytics
- 🔜 v2.0.0: Multi-agente especializado

---