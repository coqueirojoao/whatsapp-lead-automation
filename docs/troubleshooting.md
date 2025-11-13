# Troubleshooting - Soluções de Problemas Comuns

Guia rápido para resolver os problemas mais comuns.

## Índice
- [Docker](#docker)
- [WAHA](#waha)
- [n8n](#n8n)
- [TypeBot](#typebot)
- [Google Sheets](#google-sheets)
- [Integração](#integração)

## Docker

### Docker não inicia

**Problema:** Docker Desktop não abre ou fica carregando infinitamente

**Soluções:**
```bash
# 1. Reinicie o serviço do Docker
# Windows: Abra PowerShell como Administrador
net stop com.docker.service
net start com.docker.service

# 2. Limpe cache e reinicie
docker system prune -a
```

### WSL 2 não está instalado

**Problema:** Erro ao instalar Docker - "WSL 2 installation is incomplete"

**Solução:**
```powershell
# Execute no PowerShell como Administrador
wsl --install
wsl --set-default-version 2
```

Reinicie o computador após.

### Porta já em uso

**Problema:** `Error: bind: address already in use`

**Solução:**
```bash
# Descobrir o que está usando a porta
netstat -ano | findstr :3000

# Matar o processo (substitua PID)
taskkill /PID numero_do_pid /F
```

Ou altere a porta no `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"  # Usa porta 3001 em vez de 3000
```

## WAHA

### QR Code não aparece

**Problema:** Não consigo ver o QR Code para conectar WhatsApp

**Soluções:**
1. Acesse: http://localhost:3000
2. Vá em "Sessions" → "Create new session"
3. Nome da sessão: `default`
4. Aguarde QR Code aparecer

Se não aparecer:
```bash
# Reinicie o container
cd docker
docker-compose restart waha

# Veja os logs
docker-compose logs -f waha
```

### WhatsApp desconecta

**Problema:** WhatsApp desconecta sozinho após algum tempo

**Soluções:**
- Não use o WhatsApp Web no computador simultaneamente
- Mantenha o celular com internet estável
- Não deslogue do WhatsApp no celular

**Reconectar:**
```bash
# Limpe sessões antigas
docker-compose down -v
docker-compose up -d
# Escaneie novo QR Code
```

### Erro ao enviar mensagem

**Problema:** `Error: chat not found` ou `Session not ready`

**Verificações:**
```bash
# 1. Verifique se sessão está ativa
curl http://localhost:3000/api/sessions

# 2. Verifique formato do número
# Correto: 5511987654321@c.us
# Errado: 11987654321 ou +55 11 98765-4321
```

### Container WAHA não inicia

**Problema:** `docker-compose up -d` falha

**Solução:**
```bash
# Veja logs detalhados
docker-compose logs waha

# Recrie o container
docker-compose down
docker-compose up -d --force-recreate
```

## n8n

### Webhook não recebe dados

**Problema:** TypeBot envia mas n8n não recebe

**Verificações:**

1. **Workflow está ativo?**
   - Veja toggle no canto superior direito (deve estar verde/ON)

2. **URL do webhook está correta?**
   ```
   Formato correto: https://seu-workspace.app.n8n.cloud/webhook/lead-capture
   ```

3. **Teste manual:**
   ```bash
   curl -X POST https://seu-workspace.app.n8n.cloud/webhook/lead-capture \
     -H "Content-Type: application/json" \
     -d '{"nome":"Teste","email":"teste@email.com"}'
   ```

### Credenciais do Google expiradas

**Problema:** `Error: Invalid credentials`

**Solução:**
1. No n8n, vá em "Credentials"
2. Encontre "Google Sheets OAuth2"
3. Clique em "Reconnect"
4. Autorize novamente

### Execuções falham silenciosamente

**Problema:** Workflow não executa mas não dá erro

**Verificações:**
```
1. Vá em "Executions" no menu lateral
2. Veja execuções recentes
3. Clique em uma execução falhada
4. Veja qual nó falhou (vermelho)
5. Leia a mensagem de erro
```

### n8n local não acessa WAHA

**Problema:** HTTP Request para WAHA falha com timeout

**Solução:**

Se n8n está em Docker:
```yaml
# No docker-compose.yml, use:
url: "http://waha:3000/api/sendText"
```

Se n8n está em Cloud:
- WAHA local não é acessível
- Use ngrok ou similar para expor WAHA:
```bash
ngrok http 3000
# Use a URL fornecida pelo ngrok
```

## TypeBot

### Bot não publica

**Problema:** Erro ao clicar em "Publish"

**Soluções:**
- Verifique se todos os blocos estão configurados
- Remova blocos vazios
- Salve antes de publicar

### Webhook retorna erro

**Problema:** "Failed to send webhook"

**Verificações:**
1. URL do webhook está correta?
2. n8n workflow está ativo?
3. Teste a URL diretamente no navegador

### Variáveis não aparecem no webhook

**Problema:** Dados enviados estão vazios ou undefined

**Solução:**
- Verifique nomes das variáveis (case-sensitive)
- Certifique-se que campos foram preenchidos
- Use `{{variavel}}` (com chaves duplas)

## Google Sheets

### "Permission denied"

**Problema:** n8n não consegue acessar planilha

**Soluções:**
1. Compartilhe planilha com o email usado no OAuth2
2. Dê permissão de "Editor"
3. Reautentique credenciais no n8n

### Dados não aparecem na planilha

**Problema:** Execução bem-sucedida mas planilha vazia

**Verificações:**
- ID da planilha está correto?
- Nome da aba está correto? (usa primeira aba por padrão)
- Range está correto? (A:F para 6 colunas)

### Cabeçalhos duplicados

**Problema:** Cada execução cria nova linha de cabeçalho

**Solução:**
- Crie cabeçalhos manualmente na linha 1
- Configure n8n para "Append" (não "Create")
- Use range começando da linha 2: `A2:F`

## Integração

### Fluxo completo não funciona

**Diagnóstico passo a passo:**

1. **Teste TypeBot isolado:**
   - Acesse o bot
   - Preencha formulário
   - Deve completar sem erros

2. **Teste n8n isolado:**
   ```bash
   curl -X POST [URL_WEBHOOK] \
     -H "Content-Type: application/json" \
     -d '{"nome":"Teste"}'
   ```

3. **Teste Google Sheets:**
   - Execute workflow manualmente no n8n
   - Verifique se linha aparece no Sheets

4. **Teste WAHA:**
   ```bash
   curl -X POST http://localhost:3000/api/sendText \
     -H "Content-Type: application/json" \
     -d '{
       "session": "default",
       "chatId": "5511999999999@c.us",
       "text": "Teste"
     }'
   ```

### Dados chegam incompletos

**Problema:** Alguns campos estão vazios

**Verificações:**
- Campos são obrigatórios no TypeBot?
- Nomes das variáveis batem exatamente?
- Mapeamento no n8n está correto?

### Demora para receber WhatsApp

**Problema:** Mensagem demora vários minutos

**Causas comuns:**
- n8n free tier tem delay
- WhatsApp Web está instável
- WAHA está sobrecarregado

**Solução:**
```bash
# Reinicie WAHA
docker-compose restart waha

# Veja se há fila de mensagens
curl http://localhost:3000/api/sessions
```

## Ferramentas de Diagnóstico

### Logs importantes

```bash
# Docker logs
docker-compose logs -f waha
docker-compose logs -f n8n

# n8n executions
# Acesse interface web → Executions

# WAHA status
curl http://localhost:3000/api/sessions
```

### Teste de conectividade

```bash
# Testar se WAHA está acessível
curl http://localhost:3000

# Testar webhook n8n
curl -X POST [URL] -H "Content-Type: application/json" -d '{"test":true}'
```

### Validar JSON

Se webhook falhar, valide JSON em: https://jsonlint.com

## Quando pedir ajuda

Se nada funcionou, ao abrir issue inclua:

1. Descrição do problema
2. O que você tentou
3. Logs relevantes
4. Versões:
   ```bash
   docker --version
   docker-compose --version
   ```
5. Sistema operacional
6. Screenshots (se aplicável)

## Recursos Adicionais

- [WAHA Documentation](https://waha.devlike.pro)
- [n8n Community](https://community.n8n.io)
- [TypeBot Docs](https://docs.typebot.io)
- [Docker Troubleshooting](https://docs.docker.com/desktop/troubleshoot/overview/)
