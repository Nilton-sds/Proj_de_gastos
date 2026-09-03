const express = require('express');
const { Pool } = require('pg');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const cron = require('node-cron');
const cors = require('cors');

const app = express();
app.use(express.json());
app.use(cors());

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }
});

const JWT_SECRET = process.env.JWT_SECRET || 'chave_secreta_aqui';

// Middleware para verificar se o usuário está logado
function autenticar(req, res, next) {
  const token = req.headers['authorization'];
  if (!token) return res.status(401).json({ error: 'Acesso negado' });
  
  try {
    const decoded = jwt.verify(token.replace('Bearer ', ''), JWT_SECRET);
    req.usuarioId = decoded.id;
    next();
  } catch (err) {
    res.status(403).json({ error: 'Token inválido' });
  }
}

// 1. ROTA DE CADASTRO
app.post('/api/registrar', async (req, res) => {
  const { email, senha } = req.body;
  const hash = await bcrypt.hash(senha, 10);
  try {
    const result = await pool.query(
      'INSERT INTO usuarios (email, senha) VALUES ($1, $2) RETURNING id, email',
      [email, hash]
    );
    res.json(result.rows[0]);
  } catch (err) {
    res.status(400).json({ error: 'Email já cadastrado ou dados inválidos' });
  }
});

// 2. ROTA DE LOGIN
app.post('/api/login', async (req, res) => {
  const { email, senha } = req.body;
  const result = await pool.query('SELECT * FROM usuarios WHERE email = $1', [email]);
  const user = result.rows[0];

  if (user && await bcrypt.compare(senha, user.senha)) {
    const token = jwt.sign({ id: user.id }, JWT_SECRET, { expiresIn: '7d' });
    res.json({ token });
  } else {
    res.status(401).json({ error: 'Credenciais inválidas' });
  }
});

// 3. ROTAS DE LANÇAMENTOS (PROTEGIDAS)
app.get('/api/lancamentos', autenticar, async (req, res) => {
  const result = await pool.query(
    'SELECT * FROM lancamentos WHERE usuario_id = $1 ORDER BY ts DESC',
    [req.usuarioId]
  );
  res.json(result.rows);
});

app.post('/api/lancamentos', autenticar, async (req, res) => {
  const { category, tipo, amount, desc, status, ts } = req.body;
  const result = await pool.query(
    'INSERT INTO lancamentos (usuario_id, categoria, tipo, amount, descricao, status, ts) VALUES ($1, $2, $3, $4, $5, $6, $7) RETURNING *',
    [req.usuarioId, category, tipo, amount, desc, status, ts]
  );
  res.json(result.rows[0]);
});

app.delete('/api/lancamentos/:id', autenticar, async (req, res) => {
  await pool.query('DELETE FROM lancamentos WHERE id = $1 AND usuario_id = $2', [
    req.params.id,
    req.usuarioId
  ]);
  res.json({ success: true });
});

// 4. LIMPEZA AUTOMÁTICA MENSAL
// Executa no minuto 0 da hora 0 do dia 1 de cada mês ('0 0 1 * *')
cron.schedule('0 0 1 * *', async () => {
  console.log('Iniciando limpeza mensal de despesas...');
  try {
    // Apaga apenas os gastos/despesas, mantendo as entradas de renda (ou remova o "WHERE" para apagar tudo)
    await pool.query("DELETE FROM lancamentos WHERE tipo = 'despesa'");
    console.log('Limpeza de gastos concluída com sucesso.');
  } catch (err) {
    console.error('Erro ao realizar limpeza mensal:', err);
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Servidor rodando na porta ${PORT}`));
