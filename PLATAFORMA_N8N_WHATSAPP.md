# Plataforma Web para Disparo em Massa via WhatsApp (n8n + Google Sheets)

## Objetivo
Criar um **SaaS para PMEs** que use seu fluxo existente no n8n como motor de automação, com uma interface web simples para operação comercial e controle.

## Proposta de Produto (MVP)

### Perfis de usuário
- **Admin da empresa**: configura conexão com WhatsApp, equipe e limites.
- **Operador**: importa/seleciona contatos e dispara campanhas.
- **Gestor**: acompanha relatórios e performance.

### Funcionalidades essenciais
1. **Autenticação e multiempresa (multi-tenant)**
   - Cadastro/login por e-mail e senha.
   - Cada cliente vê apenas seus dados.
2. **Gestão de contatos**
   - Importar contatos por Google Sheets (URL/ID da planilha).
   - Validação de telefone (formato E.164) e deduplicação.
3. **Campanhas**
   - Criar campanha com nome, template de mensagem e variáveis (`{{nome}}`, `{{cidade}}`).
   - Agendar disparo ou executar imediatamente.
4. **Integração com n8n**
   - Botão “Iniciar campanha” chama um webhook do n8n com payload padronizado.
   - Receber retorno por webhook de status (enviado/falha).
5. **Relatórios básicos**
   - Total de contatos, enviados, falhas, taxa de entrega.
   - Histórico por campanha.
6. **LGPD e segurança mínima**
   - Consentimento e base legal no cadastro do contato.
   - Auditoria simples de ações (quem disparou, quando, qual campanha).

## Arquitetura recomendada

### Front-end
- **Next.js + TypeScript**
- UI com Tailwind + componentes padrão.

### Back-end
- **NestJS ou Fastify (Node.js)**
- API REST com autenticação JWT.
- Fila de jobs (BullMQ + Redis) para controlar lotes e rate limit.

### Banco de dados
- **PostgreSQL**
- Tabelas principais: `tenants`, `users`, `contacts`, `campaigns`, `campaign_messages`, `audit_logs`.

### Automação
- **n8n** continua sendo o orquestrador de envio.
- Plataforma envia dados para um webhook de entrada e consome webhook de retorno para atualizar status.

## Fluxo operacional sugerido
1. Usuário cria campanha no painel.
2. Sistema busca contatos da Google Sheets (ou contatos já salvos).
3. Sistema prepara lotes (ex.: 100 por batch) e coloca em fila.
4. Worker dispara webhook do n8n por lote.
5. n8n envia mensagens no WhatsApp provider.
6. n8n retorna status por contato para a API.
7. Dashboard atualiza métricas em tempo real.

## Estratégia comercial para PMEs

### Planos
- **Starter**: até X contatos/mês, 1 usuário.
- **Growth**: mais contatos, múltiplos usuários, agendamento.
- **Scale**: API, suporte prioritário, SLA.

### Diferenciais que vendem
- “Não precisa mexer no n8n”.
- Interface simples para time comercial.
- Relatórios claros de ROI por campanha.
- Suporte a compliance (LGPD e opt-out).

## Roadmap de implementação (90 dias)

### Fase 1 (Semanas 1-3): Fundação
- Setup de autenticação e multi-tenant.
- CRUD de contatos e campanhas.
- Integração inicial com Google Sheets.

### Fase 2 (Semanas 4-6): Integração n8n + disparo
- Webhook de saída para n8n.
- Recebimento de status por webhook.
- Dashboard inicial com métricas básicas.

### Fase 3 (Semanas 7-9): Produto vendável
- Gestão de plano e cobrança (ex.: Stripe/Asaas).
- Limites por plano e controle de uso.
- Hardening de segurança e auditoria.

### Fase 4 (Semanas 10-12): Go-to-market
- Landing page com trial.
- Onboarding guiado em 5 passos.
- Métricas de ativação e retenção.

## Modelo mínimo de payload (plataforma -> n8n)

```json
{
  "tenantId": "empresa_123",
  "campaignId": "camp_456",
  "messageTemplate": "Olá {{nome}}, temos uma oferta para {{cidade}}.",
  "contacts": [
    { "id": "c1", "name": "Maria", "phone": "+5511999999999", "cidade": "SP" },
    { "id": "c2", "name": "João", "phone": "+5511988888888", "cidade": "Campinas" }
  ],
  "options": {
    "rateLimitPerMinute": 60,
    "respectBusinessHours": true
  }
}
```

## Próximos passos práticos
1. Validar stack (Next.js + Node + Postgres + Redis + n8n).
2. Definir provider oficial de WhatsApp (Cloud API ou parceiro BSP).
3. Construir MVP com foco em:
   - importar contatos,
   - criar campanha,
   - disparar via webhook,
   - visualizar status.
4. Piloto com 2-3 PMEs para ajustes antes de escalar vendas.

---

Se você quiser, no próximo passo eu posso transformar isso em uma **especificação técnica completa** (entidades, endpoints e wireframes) para seu time começar a desenvolver imediatamente.
