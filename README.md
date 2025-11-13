# WhatsApp Lead Automation

Sistema completo de automação para captura de leads através de chatbot com notificações via WhatsApp.

## Sobre o Projeto

Este projeto integra TypeBot, n8n e WAHA (WhatsApp HTTP API) para criar um sistema automatizado de atendimento e captura de leads. Quando um visitante interage com o chatbot, os dados são processados automaticamente e você recebe uma notificação no WhatsApp.

## Tecnologias Utilizadas

- **TypeBot** - Chatbot conversacional no-code
- **n8n** - Plataforma de automação de workflows
- **WAHA** - WhatsApp HTTP API (self-hosted)
- **Google Sheets** - Armazenamento de dados dos leads
- **Docker** - Containerização e deploy

## Arquitetura

```
Cliente → TypeBot (Interface do Chat)
    ↓
  Webhook
    ↓
  n8n (Orquestração)
    ↓
  ├─→ Google Sheets (Armazena Lead)
  └─→ WAHA (Envia WhatsApp)
```

## Funcionalidades

- ✅ Chatbot interativo para qualificação de leads
- ✅ Captura automática de dados (nome, email, telefone, interesse)
- ✅ Armazenamento em Google Sheets
- ✅ Notificação instantânea via WhatsApp
- ✅ Workflow 100% automatizado
- ✅ Solução gratuita e self-hosted

## Estrutura do Projeto

```
whatsapp-lead-automation/
├── docker/
│   └── docker-compose.yml       # Configuração WAHA + n8n (opcional)
├── n8n/
│   └── workflows/
│       └── lead-workflow.json   # Workflow de automação
├── typebot/
│   └── bot-export.json          # Configuração do chatbot
├── docs/
│   ├── setup-guide.md           # Guia de instalação
│   └── screenshots/             # Capturas de tela
└── README.md
```

## Pré-requisitos

- Docker Desktop instalado
- Conta Google (para Google Sheets)
- Número WhatsApp
- Conta n8n Cloud (tier gratuito) ou n8n self-hosted
- Conta TypeBot (plano gratuito)

## Instalação

Documentação completa de instalação disponível em [docs/setup-guide.md](docs/setup-guide.md)

### Quick Start

1. Clone o repositório:
```bash
git clone https://github.com/coqueirojoao/whatsapp-lead-automation.git
cd whatsapp-lead-automation
```

2. Suba o WAHA com Docker:
```bash
cd docker
docker-compose up -d
```

3. Configure o TypeBot (veja guia completo)
4. Configure o n8n (veja guia completo)
5. Importe o workflow do n8n
6. Teste o fluxo completo

## Configuração

Detalhes de configuração de cada componente em [docs/setup-guide.md](docs/setup-guide.md)

## Como Usar

1. Acesse o TypeBot publicado
2. Interaja com o chatbot
3. Preencha as informações solicitadas
4. Os dados serão salvos automaticamente no Google Sheets
5. Você receberá uma notificação no WhatsApp

## Demonstração

_Screenshots e vídeos serão adicionados em breve_

## Casos de Uso

- 💼 Captura de leads para vendas
- 🎯 Qualificação de prospects
- 📊 Pesquisas de satisfação
- 🤖 Atendimento automatizado
- 📈 Agendamento de reuniões

## Custos

Este projeto é **100% gratuito**:
- TypeBot: Plano gratuito (200 chats/mês)
- n8n Cloud: Tier gratuito (200 execuções/mês)
- WAHA: Open-source gratuito
- Google Sheets: Gratuito

## Roadmap

- [ ] Setup inicial do projeto
- [ ] Configuração WAHA
- [ ] Criação do chatbot TypeBot
- [ ] Workflow n8n completo
- [ ] Integração Google Sheets
- [ ] Integração WhatsApp
- [ ] Documentação completa
- [ ] Screenshots e demonstrações

## Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou pull requests.

## Licença

Este projeto é open-source e está disponível sob a licença MIT.

## Autor

Desenvolvido como projeto de demonstração para automação de processos com ferramentas no-code/low-code.

## Agradecimentos

- [TypeBot](https://typebot.io) - Plataforma de chatbot
- [n8n](https://n8n.io) - Plataforma de automação
- [WAHA](https://waha.devlike.pro) - WhatsApp API
