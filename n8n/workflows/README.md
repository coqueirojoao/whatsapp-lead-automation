# n8n Workflows

Este diretório contém os workflows do n8n para automação.

## Workflow Principal: Lead Capture

Arquivo: `lead-workflow.json`

### Visão Geral

Este workflow recebe dados do TypeBot, processa, salva no Google Sheets e envia notificação via WhatsApp.

### Nós do Workflow

#### 1. Webhook (Trigger)
- **Tipo:** Webhook
- **Método:** POST
- **Path:** `lead-capture`
- **Autenticação:** None
- **Resposta:** Immediately

**Configuração:**
```json
{
  "httpMethod": "POST",
  "path": "lead-capture",
  "responseMode": "onReceived",
  "responseData": "firstEntryJson"
}
```

#### 2. Set (Processar Dados)
- **Tipo:** Set
- **Função:** Formatar e preparar dados

**Campos:**
- `nome`: `{{$json.nome}}`
- `email`: `{{$json.email}}`
- `telefone`: `{{$json.telefone}}`
- `interesse`: `{{$json.interesse}}`
- `data`: `{{$now.format('DD/MM/YYYY HH:mm:ss')}}`
- `origem`: `typebot`

#### 3. Google Sheets (Salvar Lead)
- **Tipo:** Google Sheets
- **Operação:** Append Row
- **Range:** A:F

**Credenciais:**
1. OAuth2 Google
2. Permissões: Google Sheets API

**Mapeamento:**
- Coluna A: `{{$json.nome}}`
- Coluna B: `{{$json.email}}`
- Coluna C: `{{$json.telefone}}`
- Coluna D: `{{$json.interesse}}`
- Coluna E: `{{$json.data}}`
- Coluna F: `{{$json.origem}}`

#### 4. HTTP Request (Enviar WhatsApp via WAHA)
- **Tipo:** HTTP Request
- **Método:** POST
- **URL:** `http://localhost:3000/api/sendText`

**Body:**
```json
{
  "session": "default",
  "chatId": "5511999999999@c.us",
  "text": "🔔 *Novo Lead Recebido!*\n\n👤 *Nome:* {{$json.nome}}\n📧 *Email:* {{$json.email}}\n📱 *Telefone:* {{$json.telefone}}\n🎯 *Interesse:* {{$json.interesse}}\n🕐 *Data:* {{$json.data}}"
}
```

**Headers:**
```json
{
  "Content-Type": "application/json"
}
```

### Fluxo de Execução

```
TypeBot → Webhook → Set → [Google Sheets, HTTP Request (WAHA)]
                            ↓               ↓
                      Sheets Salvo    WhatsApp Enviado
```

## Como Importar o Workflow

### Via Interface n8n

1. Acesse seu n8n (Cloud ou local)
2. Clique em "..." (menu) → "Import from File"
3. Selecione `lead-workflow.json`
4. Configure as credenciais necessárias:
   - Google Sheets OAuth2
   - (Webhook não precisa de credenciais)

### Via URL (se disponível)

1. Copie o conteúdo do `lead-workflow.json`
2. No n8n, vá em "Import from URL"
3. Cole o JSON

## Configurações Necessárias

### 1. Credencial Google Sheets

1. No n8n, vá em "Credentials" → "Create New"
2. Selecione "Google Sheets OAuth2 API"
3. Siga o fluxo de autenticação
4. Autorize acesso às planilhas

### 2. URL do WAHA

Se WAHA estiver:
- **Local (Docker):** `http://localhost:3000/api/sendText`
- **n8n Cloud conectando WAHA local:** Use ngrok ou tunelamento
- **WAHA em servidor:** `http://seu-ip:3000/api/sendText`

### 3. Número WhatsApp

Substitua `5511999999999@c.us` pelo seu número:
- Formato: `DDDnumero@c.us`
- Exemplo: `5511987654321@c.us` (SP)
- Exemplo: `5521987654321@c.us` (RJ)

## Como Exportar o Workflow

Para salvar modificações:

1. No n8n, abra o workflow
2. Clique em "..." → "Download"
3. Salve como `lead-workflow.json` neste diretório
4. Faça commit das alterações

## Testes

### Teste Manual via Interface

1. Abra o workflow no n8n
2. Clique em "Execute Workflow"
3. No painel "Webhook", clique em "Listen for test event"
4. Acesse o TypeBot e preencha
5. Veja os dados chegarem em tempo real

### Teste via cURL

```bash
curl -X POST https://seu-n8n.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Silva",
    "email": "joao@email.com",
    "telefone": "11987654321",
    "interesse": "Produto A",
    "timestamp": "2025-11-13T10:30:00"
  }'
```

### Verificar Execuções

1. No n8n, vá em "Executions"
2. Veja histórico de execuções
3. Clique em uma execução para ver detalhes
4. Verifique cada nó (verde = sucesso, vermelho = erro)

## Monitoramento

### Logs de Execução

- Acesse "Executions" no menu lateral
- Filtre por:
  - Status (Success/Error)
  - Data
  - Workflow

### Notificações de Erro

Configure no workflow um nó de erro:
1. Adicione "Error Trigger"
2. Configure ação (ex: enviar email de erro)

## Melhorias Futuras

### Validações

Adicione nós de validação:
- IF para validar email
- IF para validar telefone
- Switch para categorizar leads

### Enriquecimento

- Adicionar busca de CEP
- Integrar com APIs de dados
- Análise de sentimento

### CRM Integration

- Salvar em CRM (Pipedrive, HubSpot)
- Criar deal automático
- Atribuir responsável

### Analytics

- Enviar eventos para Google Analytics
- Tracking de conversão
- Dashboard de métricas

## Troubleshooting

### Webhook não recebe dados
- Verifique se workflow está ativo (toggle ON)
- Teste URL do webhook no navegador
- Veja logs de execução

### Google Sheets não salva
- Reautentique credenciais
- Verifique permissões
- Confirme ID da planilha

### WhatsApp não envia
- Verifique se WAHA está rodando
- Teste API do WAHA diretamente
- Confirme formato do número

### Erros de Timeout
- Aumente timeout no HTTP Request
- Verifique conectividade de rede

## Recursos

- [Documentação n8n](https://docs.n8n.io)
- [Workflow Templates](https://n8n.io/workflows)
- [Comunidade n8n](https://community.n8n.io)
- [n8n Academy](https://docs.n8n.io/courses/)
