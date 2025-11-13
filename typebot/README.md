# TypeBot Configuration

Este diretório contém a configuração e exportação do chatbot TypeBot.

## Estrutura do Chatbot

### Fluxo de Conversação

1. **Boas-vindas** - Mensagem inicial
2. **Captura de Nome** - Input de texto
3. **Captura de Email** - Input de email
4. **Captura de Telefone** - Input de telefone
5. **Interesse** - Botões de múltipla escolha
6. **Webhook** - Envio de dados para n8n
7. **Agradecimento** - Mensagem final

## Variáveis Utilizadas

- `nome` - Nome do lead
- `email` - Email do lead
- `telefone` - Telefone com DDD
- `interesse` - Tipo de interesse/produto
- `timestamp` - Data/hora do contato

## Configuração do Webhook

### URL do Webhook
Configurar no bloco 6 com a URL fornecida pelo n8n:
```
https://seu-workspace.app.n8n.cloud/webhook/lead-capture
```

### Payload JSON
```json
{
  "nome": "{{nome}}",
  "email": "{{email}}",
  "telefone": "{{telefone}}",
  "interesse": "{{interesse}}",
  "timestamp": "{{now}}"
}
```

## Como Importar

1. Acesse seu TypeBot
2. Vá em "Settings" → "Import/Export"
3. Faça upload do arquivo `bot-export.json`
4. Ajuste o webhook URL
5. Publique o bot

## Como Exportar

Para salvar suas modificações:

1. No TypeBot, vá em "Settings" → "Import/Export"
2. Clique em "Export"
3. Salve o arquivo como `bot-export.json` neste diretório
4. Faça commit das alterações

## Personalização

### Modificar Perguntas

Edite os blocos de texto para adequar ao seu negócio:
- Boas-vindas
- Perguntas
- Mensagem final

### Adicionar/Remover Campos

1. No TypeBot, adicione novos blocos de input
2. Crie novas variáveis
3. Atualize o payload do webhook
4. Atualize o workflow do n8n para processar novos campos

### Customizar Aparência

No TypeBot, vá em "Theme" e ajuste:
- Cores
- Fonte
- Avatar
- Background

## Integração com Website

### Embed Script

```html
<script type="module">
  import Typebot from 'https://cdn.typebot.io/latest/web.js'

  Typebot.initBubble({
    typebot: "seu-bot-id",
    theme: {
      button: { backgroundColor: "#0042DA" }
    }
  })
</script>
```

### Popup

```html
<script type="module">
  import Typebot from 'https://cdn.typebot.io/latest/web.js'

  Typebot.initPopup({
    typebot: "seu-bot-id",
    autoShowDelay: 3000
  })
</script>
```

## Testes

### Testar Localmente
1. Acesse o link de preview do TypeBot
2. Preencha todos os campos
3. Verifique se webhook foi enviado (veja logs do n8n)

### Testar em Produção
1. Publique o bot
2. Acesse URL pública
3. Faça teste completo
4. Verifique Google Sheets e WhatsApp

## Métricas

No dashboard do TypeBot você pode acompanhar:
- Total de conversas
- Taxa de conclusão
- Tempo médio de conversa
- Campos mais abandonados

## Limites do Plano Gratuito

- 200 chats/mês
- Typebots ilimitados
- Webhooks inclusos
- Custom JS/CSS

Se precisar de mais:
- Plano Starter: $39/mês (2.000 chats)
- Plano Pro: $89/mês (10.000 chats)

## Recursos

- [Documentação TypeBot](https://docs.typebot.io)
- [Templates](https://typebot.io/templates)
- [Comunidade Discord](https://discord.gg/typebot)
