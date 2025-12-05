# nInA — Documentação de Uso  
Agente de IA do Sistema FIEC

## 📌 Introdução

A **nInA** é um assistente virtual inteligente criado para fornecer respostas rápidas, consistentes e baseadas nas políticas, normas e documentos internos do Sistema FIEC.

**Propósito:** Interpretar e explicar documentos internos, garantindo uniformidade e precisão.  
**Motivação:** Reduzir dúvidas recorrentes e inconsistências de interpretação.  
**Valor do MVP:** Menos carga operacional, consulta padronizada e maior agilidade no dia a dia.  
**Público-Alvo Inicial:** Time interno do Observatório e usuários que trabalham com políticas e normas internas.

---

## 🛠️ Como Usar a nInA no Google Chat

A nInA está integrada ao Google Chat e pode ser usada em **grupos** ou **conversas individuais**.

---

### 💬 Uso em Grupos / Espaços (Recomendado)

1. Acesse o espaço onde a nInA já está adicionada.  
   Exemplo de espaço de testes:  
   `https://chat.google.com/room/AAQAoqGdYcM?cls=1`

2. Para fazê-la responder, mencione:  
   **@nInA (Agente de IA do Sistema FIEC)**

3. Escreva sua pergunta e envie.

A nInA responderá no mesmo espaço, te mencionando.

---

### 👤 Uso em Conversa Individual (DM)

#### Como acessar

**Método atual (teste):**
- Clique no avatar da nInA dentro do espaço de testes.
- Selecione **Instalar aplicativo** → você será levado ao chat privado.

**Método oficial (próximo passo):**
- Acesse a aba **APPS** no Google Chat.
- Pesquise por **"nInA (Agente de IA do Sistema FIEC)"**.
- Abra o app para iniciar a conversa privada.

#### Como usar no DM
- Basta enviar sua pergunta.
- Não precisa mencionar @nInA em conversas privadas.

---

## 💡 Exemplos de Uso (Casos Reais)

| Cenário | Documento(s) | Pergunta Exemplo | Objetivo |
|--------|---------------|------------------|----------|
| Princípios de Qualidade | Política de Qualidade de Dados | “Quais são os princípios orientadores da política?” | Resumo claro das diretrizes. |
| Padrões de Metadados | Norma de Uso de Metadados Institucionais | “Quais são os campos de metadados da FIEC?” | Obter a lista e descrições completas. |
| Criação de Termos | Modelo de Formulário + Norma de Glossário | “Quero criar um termo. Como faço?” | Entender fluxo e campos necessários. |
| Ciclo de Vida do Dado | Política de Ciclo de Vida de Dados | “O que é ciclo de vida do dado?” | Esclarecer conceitos e etapas. |

---

## ⚙️ Integrações

### Google Chat  
Canal principal de interação.

### Google Gemini Enterprise  
Motor de IA generativa, com segurança e IAM corporativo.

### N8N Community  
Orquestra workflows e faz parte da infraestrutura de deploy.

---

## 🛑 Políticas e Diretrizes

### Política de Privacidade
A nInA coleta apenas dados essenciais para funcionamento e segurança:  
- perguntas, respostas, registros de uso, logs técnicos.  
Tudo armazenado de forma segura no ambiente FIEC + Google Workspace.  
Não há compartilhamento externo, exceto para operação essencial ou exigência legal.  
O usuário pode solicitar informações sobre seus dados conforme legislação aplicável.

---

### Termos de Uso
Ao utilizar a nInA, o usuário concorda em:
- Usar o serviço de forma responsável e alinhada às políticas internas.  
- Não realizar usos maliciosos (exploração, prompts hostis, spam, etc).

O serviço é fornecido **"como está"**, podendo haver instabilidades ou manutenções.

---

### Diretrizes da Comunidade
- Ferramenta de uso **interno**.  
- Interações devem ser profissionais e construtivas.  
- Proibido: conteúdos ofensivos, ilegais, uso pessoal, spam ou exploração de falhas.  
- Problemas devem ser reportados ao suporte.

---

## ⚠️ Casos de Erro

### “Não obtive uma resposta”
**Causa:** A nInA não foi mencionada em grupo.  
**Solução:** Sempre use **@nInA** em espaços.

### “A resposta é genérica ou vaga”
**Causa:** Pergunta muito ampla ou fora do escopo.  
**Solução:** Refine trazendo termos dos documentos internos.

### “Erro de rede / serviço indisponível”
**Causa:** Instabilidade do Google Chat ou backend.  
**Solução:** Tente novamente depois. Persistindo, contate o suporte.

---

## 🚨 Limitações Conhecidas

### 1. Foco Exclusivo em Documentos Internos
A nInA não responde temas externos.  
**Workaround:** Formule perguntas referenciando documentos internos.

### 2. Linguagem Natural com Variabilidade
Perguntas muito vagas ou com gírias podem afetar a interpretação.  
**Workaround:** Use linguagem clara e direta.

### 3. Escopo do MVP (Data Governance)
Atualmente especializada em:  
- Metadados  
- Glossário  
- Ciclo de Vida  
- Qualidade de Dados  
**Próximos passos:** expansão para novos temas e agentes especializados.

---

## 💻 Para Desenvolvedores / Time Técnico

### Arquitetura (Visão Geral)
- Frontend: Google Chat  
- Orquestração: N8N Community (Docker Compose)  
- IA: Google Gemini Enterprise

### Modelos
- Base IA totalmente fornecida pelo Gemini Enterprise.

### Treinamento e Atualização
- Arquivos/documentos internos processados previamente.

### APIs e Endpoints
- Construída sobre infraestrutura Google e integrações compatíveis.

### Deploy e Infraestrutura
- Deploy via GitLab CI/CD  
- Validação automática: `docker compose config`  
- Rastreabilidade total a cada alteração

### Performance
- Gemini Enterprise garante desempenho alto, segurança e custo controlado.

---

