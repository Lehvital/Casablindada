import express from 'express';
import cors from 'cors';
import crypto from 'crypto';
import { createClient } from '@supabase/supabase-js';
import axios from 'axios';
import dotenv from 'dotenv';

dotenv.config();

const app = express();
const PORT = process.env.PORT || 3001;

// Middleware
app.use(express.json());
app.use(cors({ origin: '*' }));

// Supabase
const supabaseUrl = process.env.SUPABASE_URL;
const supabaseKey = process.env.SUPABASE_ANON_KEY;
const supabase = supabaseUrl && supabaseKey ? createClient(supabaseUrl, supabaseKey) : null;

// Z-API
const zapiInstance = process.env.ZAPI_INSTANCE;
const zapiToken = process.env.ZAPI_TOKEN;
const zapiClientToken = process.env.ZAPI_CLIENT_TOKEN;
const zapiEnabled = zapiInstance && zapiToken && zapiClientToken;

// Kiwify
const kiwifySecret = process.env.KIWIFY_WEBHOOK_SECRET;
const productLink = process.env.PRODUCT_LINK;

// ─── HEALTH CHECK ───────────────────────────────────────────────────────────
app.get('/health', (req, res) => {
  res.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
    supabase: !!supabase,
    zapi: zapiEnabled,
    kiwify: !!kiwifySecret
  });
});

// ─── WEBHOOK KIWIFY ─────────────────────────────────────────────────────────
app.post('/api/webhook', async (req, res) => {
  try {
    const signature = req.headers['x-signature'];
    const body = JSON.stringify(req.body);

    // Verificar assinatura
    if (kiwifySecret && signature) {
      const hash = crypto
        .createHmac('sha256', kiwifySecret)
        .update(body)
        .digest('hex');
      
      if (hash !== signature) {
        console.warn('⚠️ Assinatura inválida');
        return res.status(401).json({ error: 'Invalid signature' });
      }
    }

    const event = req.body;
    console.log('📩 Webhook recebido:', event.type);

    // Sale approved
    if (event.type === 'sale.approved' || event.type === 'order.completed') {
      const sale = event.data || event;
      const customerName = sale.customer_name || sale.customer?.name || 'Cliente';
      const customerEmail = sale.customer_email || sale.customer?.email || '';
      const customerPhone = sale.customer_phone || sale.customer?.phone || '';
      const productName = sale.product_name || 'Produto';
      const saleValue = sale.amount || sale.total || 0;

      console.log(`✅ Venda aprovada: ${customerName} — R$${saleValue}`);

      // 1️⃣ Registrar no Supabase
      if (supabase) {
        try {
          const { error } = await supabase.from('vendas').insert({
            nome: customerName,
            email: customerEmail,
            telefone: customerPhone,
            produto: productName,
            valor: saleValue,
            status: 'aprovada',
            data: new Date().toISOString()
          });
          if (error) console.error('❌ Supabase error:', error);
          else console.log('☁️ Venda registrada no Supabase');
        } catch (e) {
          console.error('❌ Supabase conexão falhou:', e.message);
        }
      }

      // 2️⃣ Enviar WhatsApp via Z-API
      if (zapiEnabled && customerPhone) {
        try {
          const wppNumber = customerPhone.replace(/\D/g, '');
          const message = `🎉 Bem-vindo!\n\nObrigado pela compra de "${productName}".\n\nSeu acesso foi liberado! 🔓\n\nQualquer dúvida, estamos aqui. 👊`;
          
          const zapiUrl = `https://api.z-api.io/instances/${zapiInstance}/token/${zapiToken}/send-message`;
          const payload = {
            phone: `55${wppNumber}`,
            message: message
          };

          await axios.post(zapiUrl, payload, {
            headers: { 'Client-Token': zapiClientToken }
          });
          console.log('💬 WhatsApp enviado');
        } catch (e) {
          console.error('❌ Z-API error:', e.message);
        }
      }

      // 3️⃣ Pixel Meta
      if (process.env.META_PIXEL_ID) {
        try {
          // Pixel disparado via client-side (no HTML do painel)
          console.log('📊 Pixel Meta disparado (client-side)');
        } catch (e) {
          console.error('❌ Pixel error:', e.message);
        }
      }

      return res.json({ success: true, message: 'Venda processada com sucesso' });
    }

    return res.json({ received: true });
  } catch (error) {
    console.error('❌ Webhook error:', error.message);
    res.status(400).json({ error: error.message });
  }
});

// ─── STATUS ─────────────────────────────────────────────────────────────────
app.get('/api/status', (req, res) => {
  res.json({
    supabase: supabase ? 'Conectado ✅' : 'Desconectado ❌',
    zapi: zapiEnabled ? 'Ativo ✅' : 'Inativo ❌',
    kiwify: kiwifySecret ? 'Configurado ✅' : 'Não configurado ❌',
    timestamp: new Date().toISOString()
  });
});

// ─── 404 ────────────────────────────────────────────────────────────────────
app.use((req, res) => {
  res.status(404).json({ error: 'Endpoint não encontrado' });
});

// ─── START ───────────────────────────────────────────────────────────────────
app.listen(PORT, () => {
  console.log(`
╔════════════════════════════════════════════╗
║     🏠 CASA BLINDADA API v3 — ATIVA       ║
║     🚀 Rodando na porta ${PORT}              ║
║     📊 http://localhost:${PORT}/health      ║
╚════════════════════════════════════════════╝
  `);
});

export default app;
