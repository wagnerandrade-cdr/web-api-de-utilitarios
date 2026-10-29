# web-api-de-utilitarios
resposta do exercicio

corpo da requisição "json"
{
  "input": "Lorem Ipsum"
}

reposta (jason)
{
  "action": "lowercase",
  "input": "Lorem Ipsum",
  "output": "lorem ipsum"
}

metodo get

{
  "action": "minimum",
  "input": ["10", "1", "100"],
  "output": 1
}

codigo em node.js usando o express.js

const express = require('express');
const app = express();
const PORT = 3000;

// Middleware para processar requisições em formato JSON
app.use(express.json());

// --- Utilitário de Texto ---
app.post('/util/text/:action', (req, res) => {
  const { action } = req.params;
  const { input } = req.body;

  if (typeof input !== 'string') {
    return res.status(400).json({ error: 'O campo "input" deve ser uma string.' });
  }

  let output;

  if (action === 'lowercase') {
    output = input.toLowerCase();
  } else if (action === 'uppercase') {
    output = input.toUpperCase();
  } else {
    return res.status(400).json({ error: 'Ação inválida para Utilitário de Texto. Use "lowercase" ou "uppercase".' });
  }

  return res.json({
    action,
    input,
    output
  });
});

// --- Utilitário de Número ---
app.get('/util/number/:action', (req, res) => {
  const { action } = req.params;
  const { input } = req.query;

  if (!input) {
    return res.status(400).json({ error: 'O parâmetro de busca "input" é obrigatório.' });
  }

  // Separa a string de entrada em um array de strings
  const inputList = input.split(',');
  
  // Converte a lista de strings para números reais para o cálculo do mínimo/máximo
  const numbers = inputList.map(item => Number(item.trim()));

  // Valida se todos os itens convertidos são números válidos
  if (numbers.some(isNaN)) {
    return res.status(400).json({ error: 'Todos os valores enviados no "input" devem ser números válidos.' });
  }

  let output;

  if (action === 'minimum') {
    output = Math.min(...numbers);
  } else if (action === 'maximum') {
    output = Math.max(...numbers);
  } else {
    return res.status(400).json({ error: 'Ação inválida para Utilitário de Número. Use "minimum" ou "maximum".' });
  }

  return res.json({
    action,
    input: inputList,
    output
  });
});

// Inicialização do servidor
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
