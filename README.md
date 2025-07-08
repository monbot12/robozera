<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>RobôZera Pro - Double</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: linear-gradient(to bottom, #1c1c1c, #400000);
      color: #fff;
      text-align: center;
      padding-top: 40px;
    }
    h1 {
      font-size: 28px;
      margin-bottom: 10px;
    }
    #previsao {
      font-size: 22px;
      margin-top: 15px;
    }
    button {
      padding: 10px 20px;
      background: #ff0000;
      color: #fff;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
      margin-top: 25px;
    }
  </style>
</head>
<body>
  <h1>🎯 RobôZera Pro - Double</h1>
  <div id="previsao">Carregando previsão...</div>
  <button onclick="atualizar()">🔁 Atualizar Agora</button>

  <script>
    async function atualizar() {
      document.getElementById('previsao').innerText = 'Buscando...';
      try {
        const res = await fetch('https://robozera-backend.onrender.com/previsao');
        const data = await res.json();
        document.getElementById('previsao').innerText = '⚡ Próxima cor: ' + data.proxima;
      } catch (err) {
        document.getElementById('previsao').innerText = 'Erro ao buscar previsão.';
      }
    }

    atualizar();
    setInterval(atualizar, 15000); // atualiza a cada 15s
  </script>
</body>
</html>const express = require('express');
const cors = require('cors');
const axios = require('axios');
const app = express();

app.use(cors());

let historico = [];

const obterHistorico = async () => {
  try {
    const res = await axios.get('https://blaze.com/api/roulette_games/recent');
    historico = res.data.map(jogo => jogo.color); // 0 = vermelho, 1 = preto, 2 = branco
  } catch (error) {
    console.log('Erro ao buscar histórico:', error.message);
  }
};

const prever = () => {
  const ultimos = historico.slice(0, 5);
  const vermelhos = ultimos.filter(cor => cor === 0).length;
  const pretos = ultimos.filter(cor => cor === 1).length;

  if (vermelhos >= 3) return '⚫ Preto';
  if (pretos >= 3) return '🔴 Vermelho';
  return '🔮 Branco (chance rara)';
};

app.get('/previsao', async (req, res) => {
  await obterHistorico();
  const proxima = prever();
  res.json({ proxima });
});

app.listen(3000, () => console.log('Servidor RobôZera rodando na porta 3000'));
