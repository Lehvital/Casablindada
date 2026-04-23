# 🏠 CASA BLINDADA — Máquina Passiva v3

Automação de vendas 24/7 com webhook + Supabase + Z-API + Pixel Meta.

## 🚀 Deploy Rápido

### 1️⃣ **GitHub** → **Vercel** (2 minutos)

```bash
# 1. Clone seu repo
git clone https://github.com/Lehvital/Casablindada.git
cd Casablindada

# 2. Instale dependências
npm install

# 3. Configure .env local (opcional para dev)
cp .env.example .env.local
# Edite .env.local com suas credenciais
```

### 2️⃣ **Deploy no Vercel**

1. Acesse [vercel.com](https://vercel.com)
2. Clique "New Project"
3. Selecione `Lehvital/Casablindada`
4. Clique "Import"

### 3️⃣ **Configurar Environment Variables** (Crítico)

No Vercel → Settings → Environment Variables:

```
KIWIFY_WEBHOOK_SECRET = seu_webhook_secret_da_kiwify
SUPABASE_URL = https://xyz.supabase.co
SUPABASE_ANON_KEY = eyJhbGci...
ZAPI_INSTANCE = instance_id
ZAPI_TOKEN = token_aqui
ZAPI_CLIENT_TOKEN = client_token
PRODUCT_LINK = https://pay.kiwify.com.br/seu_link
META_PIXEL_ID = seu_pixel_id (opcional)
```

---

## 📋 O que Cada Arquivo Faz

| Arquivo | Função |
|---------|--------|
| `api/webhook.js` | Recebe webhooks da Kiwify → Supabase + Z-API + Pixel |
| `package.json` | Dependências Node.js |
| `vercel.json` | Configuração de build Vercel |
| `.env.example` | Template de variáveis de ambiente |
| `.gitignore` | Ignora node_modules e .env |

---

## 🔗 URLs Importantes

Após deploy:

```
✅ Health Check: https://SEU-PROJETO.vercel.app/health
✅ Webhook: https://SEU-PROJETO.vercel.app/api/webhook
✅ Status: https://SEU-PROJETO.vercel.app/api/status
```

---

## 🔐 Configurar Webhook na Kiwify

1. Painel Kiwify → Configurações → Webhooks
2. Clique "Novo Webhook"
3. **URL**: `https://SEU-PROJETO.vercel.app/api/webhook`
4. **Evento**: `sale.approved`
5. **Secret**: mesmo valor de `KIWIFY_WEBHOOK_SECRET`
6. Clique "Criar"

---

## ⚙️ O Que Funciona Automaticamente

✅ **Kiwify** → Venda aprovada
  ↓
✅ **Webhook** → Recebe notificação
  ↓
✅ **Supabase** → Registra venda em banco
  ↓
✅ **Z-API** → Envia WhatsApp de boas-vindas
  ↓
✅ **Pixel Meta** → Rastreia conversão

---

## 📊 Monitorar Vendas

- **Local**: Abra o painel HTML (casa-blindada-painel-v3.html)
- **Supabase**: Acesse seu projeto → SQL Editor → SELECT * FROM vendas
- **Vercel**: Acesse https://SEU-PROJETO.vercel.app/api/status

---

## ❓ Troubleshooting

**Webhook não funciona?**
- Verifique se a URL no Vercel está correta
- Teste: `curl https://SEU-PROJETO.vercel.app/health`

**Supabase não conecta?**
- Confirme SUPABASE_URL e SUPABASE_ANON_KEY
- Execute o SQL das tabelas no SQL Editor

**Z-API não envia mensagens?**
- Verifique ZAPI_INSTANCE, ZAPI_TOKEN, ZAPI_CLIENT_TOKEN
- Teste a instância no painel da Z-API

---

## 📞 Suporte

Dúvidas? Verifique:
1. Logs do Vercel: https://vercel.com → SEU-PROJETO → Logs
2. Console Supabase
3. Status Z-API

---

**Feito com ❤️ para renda passiva 24/7**
