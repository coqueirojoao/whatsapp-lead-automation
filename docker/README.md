# Docker Configuration

Este diretório contém a configuração Docker para rodar WAHA (WhatsApp HTTP API).

## Serviços

### WAHA
- **Porta:** 3000
- **Imagem:** devlikeapro/waha
- **Função:** API REST para WhatsApp

### n8n (Opcional)
- **Porta:** 5678
- **Imagem:** n8nio/n8n
- **Função:** Plataforma de automação
- **Status:** Comentado (recomendamos usar n8n Cloud)

## Como Usar

### Iniciar WAHA

```bash
docker-compose up -d
```

### Verificar Status

```bash
docker-compose ps
```

### Ver Logs

```bash
# WAHA
docker-compose logs -f waha

# Todos os serviços
docker-compose logs -f
```

### Parar Serviços

```bash
docker-compose down
```

### Parar e Remover Volumes (Atenção: Remove dados do WhatsApp)

```bash
docker-compose down -v
```

## Acessar WAHA

Após iniciar, acesse:
- API: http://localhost:3000
- Swagger Docs: http://localhost:3000/docs

## Conectar WhatsApp

1. Acesse: http://localhost:3000
2. Vá em "Sessions"
3. Crie uma nova sessão (nome: "default")
4. Escaneie o QR Code com seu WhatsApp
5. Aguarde confirmação de conexão

## Testar API

```bash
# Listar sessões
curl http://localhost:3000/api/sessions

# Enviar mensagem de teste
curl -X POST http://localhost:3000/api/sendText \
  -H "Content-Type: application/json" \
  -d '{
    "session": "default",
    "chatId": "5511999999999@c.us",
    "text": "Teste de mensagem"
  }'
```

## Troubleshooting

### WAHA não inicia
```bash
# Verificar logs
docker-compose logs waha

# Reiniciar
docker-compose restart waha
```

### Porta já em uso
Se a porta 3000 já estiver em uso, edite o `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"  # Mudou para porta 3001
```

### Problemas com QR Code
1. Pare o container: `docker-compose down`
2. Remova volumes: `docker-compose down -v`
3. Inicie novamente: `docker-compose up -d`
4. Escaneie novo QR Code

## Usar n8n Local (Opcional)

Se preferir rodar n8n localmente em vez do Cloud:

1. Descomente a seção `n8n` no `docker-compose.yml`
2. Descomente o volume `n8n_data`
3. Inicie: `docker-compose up -d`
4. Acesse: http://localhost:5678
5. Login:
   - Usuário: admin
   - Senha: admin123

**Nota:** Recomendamos usar n8n Cloud para evitar problemas de webhook público.

## Volumes

Os dados são persistidos em volumes Docker:

- `waha_data`: Dados de autenticação do WhatsApp
- `waha_cache`: Cache do WhatsApp Web
- `n8n_data`: Workflows e configurações do n8n (se habilitado)

## Rede

Todos os serviços estão na rede `automation-network` e podem se comunicar entre si.
