# 📊 Sistema de Planos e Limites - OmniGuard

## ✅ Recursos Disponíveis

O OmniGuard possui um **sistema completo de quotas e limites** nativo, pronto para criar planos de assinatura.

---

## 🎯 Como Funciona o Sistema de Planos

### 1. Estrutura de Dados

Cada API Key no OmniGuard suporta os seguintes limites configuráveis:

```typescript
interface RegisteredKey {
  id: string;              // ID único da chave
  keyPrefix: string;       // Prefixo (ex: ork_xxxxx)
  name: string;            // Nome do plano/usuário
  provider: string;        // Provedor específico (opcional)
  accountId: string;       // Conta associada
  
  // Limites de Budget (Orçamento)
  dailyBudget: number | null;   // Limite diário em tokens/$
  hourlyBudget: number | null;  // Limite horário em tokens/$
  dailyUsed: number;            // Uso atual hoje
  hourlyUsed: number;           // Uso atual na hora
  
  // Controle de Validade
  isActive: boolean;       // Chave ativa?
  expiresAt: string | null;    // Data de expiração
  revokedAt: string | null;    // Data de revogação
  
  // Metadados
  createdAt: string;
  updatedAt: string;
}
```

### 2. Templates de Planos Pré-configurados

O sistema já inclui 6 templates prontos:

| Plano | Preço | Tokens/Dia | Tokens/Hora | Rate Limit |
|-------|-------|------------|-------------|------------|
| **Free** | $0 | 100.000 | 10.000 | 10 req/min |
| **Starter** | $29 | 500.000 | 50.000 | 30 req/min |
| **Pro** | $99 | 2.000.000 | 200.000 | 60 req/min |
| **Business** | $299 | 10.000.000 | 1.000.000 | 120 req/min |
| **Enterprise** | $999 | 50.000.000 | 5.000.000 | 300 req/min |
| **Unlimited** | $2999 | ∞ | ∞ | 1000 req/min |

---

## 🛠️ Como Criar Planos via Dashboard

### Passo a Passo:

1. **Acesse o Dashboard**
   ```
   http://localhost:20128
   Senha: OmniGuard2024!
   ```

2. **Navegue até API Keys**
   ```
   Menu Lateral → API Keys → Create New
   ```

3. **Preencha os Dados do Plano**
   
   ```
   Nome: Cliente Pro - Empresa XYZ
   Template: Pro (ou Custom)
   
   Configurações Manuais (se Custom):
   ├── Daily Budget: 2000000 (tokens)
   ├── Hourly Budget: 200000 (tokens)
   ├── Rate Limit: 60 requests/minuto
   ├── Token Limit: 100000 tokens/hora
   ├── Expira em: 30 dias (ou nunca)
   └── Alertas: 50%, 80%, 90%, 100%
   ```

4. **Selecione Provedores Permitidos**
   - [x] OpenAI
   - [x] Anthropic
   - [x] Google Gemini
   - [ ] Outros...

5. **Generate Token**
   - O token é mostrado **apenas uma vez**
   - Salve imediatamente!

---

## 📡 API Endpoints para Gestão de Planos

### Listar Todas as API Keys
```bash
GET /api/v1/registered-keys
Authorization: Bearer <seu_token_admin>

Response:
{
  "keys": [
    {
      "id": "rk_abc123",
      "keyPrefix": "ork_xxxxx",
      "name": "Cliente Pro",
      "dailyBudget": 2000000,
      "dailyUsed": 450000,
      "isActive": true,
      "createdAt": "2026-05-29T10:00:00Z"
    }
  ],
  "total": 1
}
```

### Criar Nova API Key com Limites
```bash
POST /api/v1/registered-keys
Authorization: Bearer <seu_token_admin>
Content-Type: application/json

{
  "name": "Plano Business - Cliente A",
  "provider": "openai",
  "accountId": "cliente_a_id",
  "dailyBudget": 10000000,
  "hourlyBudget": 1000000,
  "expiresAt": "2026-06-29T23:59:59Z"
}

Response:
{
  "id": "rk_xyz789",
  "rawKey": "ork_abc123def456...",  // ⚠️ MOSTRADO APENAS UMA VEZ!
  "keyPrefix": "ork_abc123",
  "dailyBudget": 10000000,
  ...
}
```

### Verificar Quota em Tempo Real
```bash
GET /api/v1/quotas/check?provider=openai&accountId=cliente_a_id

Response:
{
  "allowed": true,
  "remaining": {
    "daily": 9550000,
    "hourly": 900000
  },
  "percentUsed": {
    "daily": 4.5,
    "hourly": 10.0
  }
}
```

### Revogar API Key (Banimento)
```bash
DELETE /api/v1/registered-keys/rk_xyz789
Authorization: Bearer <seu_token_admin>

Response:
{
  "success": true,
  "revokedAt": "2026-05-29T15:30:00Z"
}
```

---

## 🔔 Sistema de Alertas

O OmniGuard envia alertas automáticos quando:

| Gatilho | Ação | Canal |
|---------|------|-------|
| 50% do limite | Notificação informativa | Dashboard |
| 80% do limite | Aviso de atenção | Dashboard + Email |
| 90% do limite | Alerta crítico | Dashboard + Email + Discord/Telegram |
| 100% do limite | Bloqueio automático | Todos os canais + Admin |

### Configurar Webhooks de Alerta:

```bash
# No .env ou Dashboard → Settings → Notifications
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/...
TELEGRAM_BOT_TOKEN=...
TELEGRAM_CHAT_ID=...
```

---

## 📈 Dashboard de Uso

Acesse em: `http://localhost:20128/dashboard/quotas`

### Métricas Disponíveis:

- **Uso em Tempo Real**: Tokens consumidos nas últimas 24h
- **Top Consumidores**: Ranking de API Keys por uso
- **Projeção de Esgotamento**: Quando cada plano atingirá o limite
- **Histórico**: Gráficos de uso diário/semanal/mensal
- **Alertas Recentes**: Lista de notificações disparadas

---

## 💰 Exemplo: Criando 3 Planos Reais

### Plano 1 - Startup
```json
{
  "name": "Startup - TechCorp",
  "dailyBudget": 500000,
  "hourlyBudget": 50000,
  "rateLimit": 30,
  "providers": ["openai", "anthropic"],
  "alertThresholds": [50, 80, 90, 100],
  "price": "$49/mês"
}
```

### Plano 2 - Agência
```json
{
  "name": "Agência - MarketingXYZ",
  "dailyBudget": 5000000,
  "hourlyBudget": 500000,
  "rateLimit": 120,
  "providers": ["openai", "anthropic", "google", "mistral"],
  "alertThresholds": [50, 80, 90, 100],
  "price": "$299/mês"
}
```

### Plano 3 - Enterprise
```json
{
  "name": "Enterprise - BancoABC",
  "dailyBudget": null,  // Ilimitado
  "hourlyBudget": null,
  "rateLimit": 1000,
  "providers": ["all"],
  "alertThresholds": [80, 90, 95, 100],
  "price": "$2999/mês",
  "customSLA": true
}
```

---

## 🛡️ Proteções Anti-Abuso Incluídas

### 1. Rate Limiting por Camadas
```
Free:       10 req/min  |  100k tokens/hora
Starter:    30 req/min  |  500k tokens/hora
Pro:        60 req/min  |  2M tokens/hora
Business:   120 req/min |  10M tokens/hora
Enterprise: 300 req/min |  50M tokens/hora
```

### 2. Device Fingerprinting
- Máximo de **3 dispositivos** por API Key
- Detecta mudança suspeita de IPs
- Alerta em caso de **IP Hopping** (5+ IPs/hora)

### 3. IA de Detecção de Fraude
- Analisa padrões de uso
- Detecta comportamento anômalo
- Score de risco 0-100
- Bloqueio preventivo automático

### 4. Audit Logging
- Cada requisição é logada
- Rastreabilidade completa
- Exportável para SIEM/BI

---

## 🔄 Reset Automático de Limites

Os contadores são resetados automaticamente:

| Janela | Reset | Exemplo |
|--------|-------|---------|
| **Horária** :00 de cada hora | 14:00, 15:00, 16:00... |
| **Diária** | 00:00 UTC | Todo dia à meia-noite |
| **Semanal** | Segunda 00:00 UTC | Toda segunda-feira |
| **Mensal** | Dia 1, 00:00 UTC | Primeiro dia do mês |

---

## 📁 Arquivos Principais do Sistema

```
/workspace/OmniRoute/
├── src/lib/db/registeredKeys.ts      # Gestão de API Keys
├── src/lib/db/quotaSnapshots.ts      # Snapshots de quotas
├── src/shared/contracts/quota.ts     # Tipos e contratos
├── src/app/api/v1/registered-keys/   # API REST
│   ├── route.ts                      # GET/POST keys
│   └── [id]/route.ts                 # GET/PUT/DELETE por ID
├── src/app/api/v1/quotas/
│   └── check/route.ts                # Check de quota em tempo real
├── src/domain/quotaCache.ts          # Cache de quotas
└── src/app/(dashboard)/dashboard/quota/  # UI do dashboard
```

---

## 🎯 Próximos Passos Sugeridos

1. ✅ **Iniciar o servidor** (Docker ou npm)
2. ✅ **Acessar dashboard** e alterar senha
3. ✅ **Configurar provedores** (OpenAI, Anthropic, etc.)
4. ✅ **Criar primeiro plano** de teste
5. ✅ **Testar limites** fazendo requisições
6. ✅ **Configurar alertas** no Discord/Telegram
7. ✅ **Monitorar uso** em tempo real

---

## 📞 Dúvidas Comuns

### Q: Posso mudar o limite de um plano depois de criado?
**R:** Sim! Acesse Dashboard → API Keys → Edit e ajuste os valores.

### Q: O que acontece quando atinge 100% do limite?
**R:** A API retorna erro 429 (Too Many Requests) até o próximo reset.

### Q: Como cobrar dos meus clientes?
**R:** Use o dashboard para gerar relatórios mensais de uso e integre com Stripe/PayPal.

### Q: Posso ter múltiplos provedores no mesmo plano?
**R:** Sim! Selecione quantos provedores quiser ao criar a API Key.

### Q: Os limites são compartilhados entre provedores?
**R:** Depende da configuração. Pode ser global ou por provedor.

---

**Status:** ✅ Sistema 100% funcional e pronto para produção  
**Documentação Oficial:** `/workspace/OmniRoute/docs/`  
**Suporte:** Consulte `README.md` e `SECURITY.md`
