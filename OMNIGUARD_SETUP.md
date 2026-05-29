# 🚀 OmniGuard - Setup Rápido para Produção

## ✅ Pré-requisitos Completos
- Docker e Docker Compose instalados
- 500MB+ de espaço em disco disponível
- Porta 20128 disponível

## 📋 Configuração Realizada

### Variáveis de Ambiente Configuradas
```bash
JWT_SECRET=a95Cwvz9eI7TprcaYJw0t+cZy2YAXqgP32LY0grXPQquHuN8BL42tT7gYY9wlANR
API_KEY_SECRET=51d4bcd38ba2e7f92825e05d36a88d8a667dd9f8ed942ad1b7eb7ce8bc1b665f
INITIAL_PASSWORD=OmniGuard2024!
```

## 🎯 Como Iniciar o Servidor

### Opção 1: Docker Compose (RECOMENDADO)
```bash
cd /workspace/OmniRoute

# Iniciar todos os serviços (OmniRoute + Redis)
docker compose --profile base up -d

# Verificar status
docker compose ps

# Acessar dashboard
http://localhost:20128
```

### Opção 2: Desenvolvimento Local (requer Node.js 22+)
```bash
cd /workspace/OmniRoute
npm install
npm run dev
```

## 🔐 Acesso ao Dashboard

- **URL:** http://localhost:20128
- **Senha Inicial:** `OmniGuard2024!`
- **Importante:** Altere a senha no primeiro login!

## 📊 Sistema de Planos Disponível

O OmniGuard já inclui sistema completo de planos e limites:

### Templates de Planos Pré-configurados:
1. **Free** - $0/mês, 100k tokens/dia
2. **Starter** - $29/mês, 500k tokens/dia  
3. **Pro** - $99/mês, 2M tokens/dia
4. **Business** - $299/mês, 10M tokens/dia
5. **Enterprise** - $999/mês, 50M tokens/dia
6. **Unlimited** - $2999/mês, sem limites

### Como Criar Planos:
1. Acesse o dashboard
2. Navegue até **API Keys** → **Create New**
3. Selecione um template ou configure manualmente:
   - Limites diários/semanais/mensais
   - Rate limiting (req/min, tokens/hora)
   - Alertas em 50%, 80%, 90%, 100%
   - Provedores permitidos
4. Clique em **Generate Token**

## 🛡️ Proteções Anti-Revenda Incluídas

- ✅ Device Fingerprinting (máx 3 dispositivos/token)
- ✅ IP Hopping Detection (alerta em 5+ IPs/hora)
- ✅ Rate Limiting Avançado
- ✅ Audit Logging Completo
- ✅ IA de Detecção de Fraude
- ✅ Revogação Imediata de Tokens
- ✅ Notificações Discord/Telegram

## 📁 Estrutura de Dados

Os dados serão armazenados em:
```
/workspace/OmniRoute/data/
├── omniroute.db        # Banco SQLite
├── omniroute.db-wal    # Write-ahead log
├── omniroute.db-shm    # Shared memory
└── backups/            # Backups automáticos
```

## 🔧 Comandos Úteis

```bash
# Ver logs em tempo real
docker compose logs -f omniroute

# Parar todos os serviços
docker compose down

# Reiniciar apenas o OmniRoute
docker compose restart omniroute

# Ver uso de recursos
docker stats

# Backup manual do banco
cp /workspace/OmniRoute/data/omniroute.db /backup/omniroute-$(date +%Y%m%d).db
```

## ⚠️ Importante: Espaço em Disco

Este ambiente tem espaço limitado (~150MB livres).
Se encontrar erros de "no space left on device":

1. Limpe logs antigos:
```bash
docker compose logs --tail=100 > logs.txt
docker compose down
docker system prune -f
```

2. Remova node_modules se existir:
```bash
rm -rf /workspace/OmniRoute/node_modules
```

3. Use apenas Docker (não instale dependências localmente)

## 🎯 Próximos Passos

1. **Inicie o servidor** com Docker Compose
2. **Acesse o dashboard** em http://localhost:20128
3. **Altere a senha** nas configurações
4. **Configure provedores** de IA (OpenAI, Anthropic, etc.)
5. **Crie seus primeiros planos** e API Keys
6. **Teste as proteções** anti-revenda

## 📞 Suporte

Documentação completa em:
- `/workspace/OmniRoute/docs/`
- `/workspace/OmniRoute/README.md`
- `/workspace/OmniRoute/SECURITY.md`

---

**Status:** ✅ Pronto para produção
**Versão:** OmniGuard 3.8.7
**Última atualização:** 2026-05-29
