# Guia de Instalação - WhatsApp Lead Automation

Este guia completo te levará do zero até ter o sistema funcionando.

## Índice

1. [Pré-requisitos](#pré-requisitos)
2. [Instalação do Docker](#instalação-do-docker)
3. [Configuração do WAHA](#configuração-do-waha)
4. [Configuração do TypeBot](#configuração-do-typebot)
5. [Configuração do n8n](#configuração-do-n8n)
6. [Configuração do Google Sheets](#configuração-do-google-sheets)
7. [Integração Completa](#integração-completa)
8. [Testes](#testes)
9. [Troubleshooting](#troubleshooting)

## Pré-requisitos

Antes de começar, certifique-se de ter:

- [ ] Windows 10/11 (64-bit)
- [ ] Docker Desktop instalado
- [ ] Conta Google (gratuita)
- [ ] Número WhatsApp (pode ser pessoal)
- [ ] Navegador moderno (Chrome/Edge/Firefox)

## Instalação do Docker

### Windows

1. Acesse: https://www.docker.com/products/docker-desktop/
2. Baixe o "Docker Desktop for Windows"
3. Execute o instalador
4. Marque "Use WSL 2 instead of Hyper-V" (recomendado)
5. Aguarde a instalação
6. **Reinicie o computador**
7. Abra o Docker Desktop e aguarde iniciar

### Verificar Instalação

```bash
docker --version
docker-compose --version
```

Você deve ver as versões instaladas.

## Configuração do WAHA

WAHA (WhatsApp HTTP API) permite conectar com WhatsApp via API REST.

### 1. Iniciar WAHA com Docker

No diretório do projeto:

```bash
cd docker
docker-compose up -d
```

### 2. Verificar se está rodando

Acesse: http://localhost:3000

Você deve ver a documentação da API do WAHA.

### 3. Conectar WhatsApp

1. Acesse: http://localhost:3000/
2. Crie uma nova sessão
3. Escaneie o QR Code com seu WhatsApp
4. Aguarde confirmação de conexão

### 4. Testar API

```bash
curl http://localhost:3000/api/sessions
```

Deve retornar suas sessões ativas.

## Configuração do TypeBot

TypeBot é a interface do chatbot que seus clientes verão.

### 1. Criar Conta

1. Acesse: https://typebot.io
2. Clique em "Sign Up"
3. Cadastre-se (gratuito)

### 2. Criar Primeiro Bot

1. No dashboard, clique em "Create a typebot"
2. Escolha "Start from scratch" ou use template "Lead Generation"

### 3. Configurar Fluxo do Chatbot

Crie o seguinte fluxo:

**Bloco 1 - Boas-vindas:**
- Tipo: Text
- Mensagem: "Olá! 👋 Sou o assistente virtual. Vou te ajudar a conhecer nossos serviços!"

**Bloco 2 - Nome:**
- Tipo: Text Input
- Pergunta: "Qual é o seu nome?"
- Variável: `nome`

**Bloco 3 - Email:**
- Tipo: Email Input
- Pergunta: "Qual seu melhor email?"
- Variável: `email`

**Bloco 4 - Telefone:**
- Tipo: Phone Number Input
- Pergunta: "Seu telefone com DDD?"
- Variável: `telefone`

**Bloco 5 - Interesse:**
- Tipo: Buttons
- Pergunta: "O que você procura?"
- Opções:
  - Produto A
  - Produto B
  - Consultoria
  - Outro
- Variável: `interesse`

**Bloco 6 - Webhook:**
- Tipo: Webhook
- URL: `[Será fornecida pelo n8n]`
- Método: POST
- Body:
```json
{
  "nome": "{{nome}}",
  "email": "{{email}}",
  "telefone": "{{telefone}}",
  "interesse": "{{interesse}}",
  "timestamp": "{{now}}"
}
```

**Bloco 7 - Agradecimento:**
- Tipo: Text
- Mensagem: "Obrigado, {{nome}}! Em breve entraremos em contato via {{telefone}} 😊"

### 4. Publicar Bot

1. Clique em "Publish"
2. Copie o link público do seu bot
3. Teste acessando o link

## Configuração do n8n

n8n é o cérebro da automação, conectando tudo.

### Opção A: n8n Cloud (Recomendado)

1. Acesse: https://n8n.io
2. Clique em "Get started for free"
3. Cadastre-se (14 dias Pro grátis + tier gratuito permanente)
4. Acesse seu workspace

### Opção B: n8n Self-Hosted

Se preferir rodar localmente (já está no docker-compose):

```bash
cd docker
docker-compose up -d
```

Acesse: http://localhost:5678

### Criar Workflow

1. No n8n, clique em "Create New Workflow"
2. Nome: "Lead Capture WhatsApp"

**Configuração dos Nós:**

#### Nó 1: Webhook (Trigger)
- Adicione nó "Webhook"
- Método: POST
- Path: `lead-capture`
- Responder: Immediately
- **Copie a URL do webhook** (ex: https://seu-n8n.app.n8n.cloud/webhook/lead-capture)

#### Nó 2: Set (Processar Dados)
- Adicione nó "Set"
- Configure campos:
  - `nome`: `{{$json.nome}}`
  - `email`: `{{$json.email}}`
  - `telefone`: `{{$json.telefone}}`
  - `interesse`: `{{$json.interesse}}`
  - `data`: `{{$now}}`

#### Nó 3: Google Sheets (Salvar)
- Adicione nó "Google Sheets"
- Operação: "Append"
- Autentique sua conta Google
- Selecione planilha (criar uma nova: "Leads WhatsApp")
- Mapeie os campos

#### Nó 4: HTTP Request (WAHA - Enviar WhatsApp)
- Adicione nó "HTTP Request"
- Método: POST
- URL: `http://localhost:3000/api/sendText` (ajustar se WAHA estiver em outro local)
- Body (JSON):
```json
{
  "session": "default",
  "chatId": "seu_numero@c.us",
  "text": "🔔 Novo Lead!\n\nNome: {{$json.nome}}\nEmail: {{$json.email}}\nTelefone: {{$json.telefone}}\nInteresse: {{$json.interesse}}\n\nData: {{$json.data}}"
}
```

3. Salve o workflow
4. Ative-o (toggle no canto superior direito)

## Configuração do Google Sheets

### 1. Criar Planilha

1. Acesse: https://sheets.google.com
2. Crie nova planilha: "Leads WhatsApp"
3. Adicione cabeçalhos na primeira linha:
   - A1: Nome
   - B1: Email
   - C1: Telefone
   - D1: Interesse
   - E1: Data/Hora

### 2. Conectar com n8n

No nó do Google Sheets no n8n:
1. Clique em "Create New Credential"
2. Autentique com sua conta Google
3. Permita acesso ao n8n
4. Selecione a planilha "Leads WhatsApp"

## Integração Completa

### 1. Conectar TypeBot ao n8n

1. No TypeBot, vá ao Bloco 6 (Webhook)
2. Cole a URL do webhook do n8n
3. Salve e republique o bot

### 2. Configurar WAHA no n8n

1. No nó HTTP Request do n8n
2. Se WAHA estiver local, use: `http://host.docker.internal:3000/api/sendText`
3. Se estiver em servidor, use o IP/domínio correto
4. Substitua `seu_numero@c.us` pelo seu número (formato: 5511999999999@c.us)

### 3. Fluxo Completo

```
Usuário preenche TypeBot
    ↓
TypeBot envia webhook para n8n
    ↓
n8n processa dados
    ↓
n8n salva no Google Sheets
    ↓
n8n envia WhatsApp via WAHA
    ↓
Você recebe notificação!
```

## Testes

### Teste 1: TypeBot Isolado
1. Acesse o link público do seu TypeBot
2. Preencha o formulário completo
3. Verifique se não há erros

### Teste 2: Webhook n8n
1. No n8n, abra o workflow
2. Clique em "Execute Workflow"
3. No TypeBot, preencha novamente
4. Verifique se o n8n recebeu os dados

### Teste 3: Google Sheets
1. Após enviar pelo TypeBot
2. Verifique se apareceu nova linha no Sheets

### Teste 4: WhatsApp
1. Verifique seu WhatsApp
2. Deve receber a mensagem com os dados do lead

### Teste Completo End-to-End
1. Abra o TypeBot
2. Preencha com dados reais
3. Envie
4. Verifique:
   - [ ] Dados no Google Sheets
   - [ ] Mensagem no WhatsApp
   - [ ] Sem erros no n8n

## Troubleshooting

### WAHA não conecta
- Verifique se Docker está rodando
- Acesse http://localhost:3000
- Reescaneie QR Code
- Verifique logs: `docker logs waha`

### n8n não recebe webhook
- Verifique URL do webhook
- Teste com Postman/Insomnia
- Veja se workflow está ativo

### Google Sheets não salva
- Reautentique credenciais
- Verifique permissões da conta
- Confira nome da planilha

### WhatsApp não envia
- Verifique se WAHA está conectado
- Confirme formato do número (5511999999999@c.us)
- Teste API do WAHA diretamente

### Erros comuns

**Erro: "Webhook not found"**
- Solução: Verifique se workflow está ativo no n8n

**Erro: "Session not found"**
- Solução: Reconecte WhatsApp no WAHA

**Erro: "Unauthorized"**
- Solução: Reautentique credenciais do Google

## Próximos Passos

- [ ] Customizar mensagens do TypeBot
- [ ] Adicionar mais campos de captura
- [ ] Criar dashboard no Sheets
- [ ] Adicionar integração com CRM
- [ ] Implementar respostas automáticas

## Suporte

Se encontrar problemas:
1. Verifique os logs do Docker
2. Teste cada componente isoladamente
3. Consulte documentação oficial de cada ferramenta

## Recursos Úteis

- [Documentação WAHA](https://waha.devlike.pro)
- [Documentação TypeBot](https://docs.typebot.io)
- [Documentação n8n](https://docs.n8n.io)
- [Comunidade n8n](https://community.n8n.io)
