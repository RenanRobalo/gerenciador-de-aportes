<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gerenciador de Aportes - Criptomoedas</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background-color: #f4f4f4;
    }
    header {
      background-color: #333;
      color: white;
      padding: 15px;
      text-align: center;
    }
    main {
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
      background: white;
      border-radius: 5px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    }
    table {
      width: 100%;
      margin-top: 20px;
      border-collapse: collapse;
    }
    table, th, td {
      border: 1px solid #ddd;
    }
    th, td {
      padding: 10px;
      text-align: center;
    }
    th {
      background-color: #333;
      color: white;
    }
    input {
      padding: 8px;
      margin: 5px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    button {
      padding: 10px 15px;
      margin-top: 10px;
      background-color: #5cb85c;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button:hover {
      background-color: #4cae4c;
    }
  </style>
</head>
<body>
  <header>
    <h1>Gerenciador de Aportes - KAS, ARB, RDNT</h1>
  </header>

  <main>
    <h2>Adicionar Aporte</h2>
    <form id="aporteForm">
      <label for="moeda">Moeda:</label>
      <select id="moeda" required>
        <option value="KAS">KAS</option>
        <option value="ARB">ARB</option>
        <option value="RDNT">RDNT</option>
      </select>
      <label for="precoCompra">Preço de Compra (US$):</label>
      <input type="number" id="precoCompra" step="0.0001" required>
      <label for="quantidade">Quantidade:</label>
      <input type="number" id="quantidade" step="0.0001" required>
      <button type="submit">Adicionar</button>
    </form>

    <h2>Resumo</h2>
    <table>
      <thead>
        <tr>
          <th>Moeda</th>
          <th>Preço Atual (US$)</th>
          <th>Preço Médio (US$)</th>
          <th>Quantidade</th>
          <th>PLR</th>
        </tr>
      </thead>
      <tbody id="tabelaMoedas">
        <tr>
          <td>KAS</td>
          <td id="precoKAS">0.0000</td>
          <td id="precoMedioKAS">0.0000</td>
          <td id="quantidadeKAS">0.0000</td>
          <td id="plrKAS">0.00%</td>
        </tr>
        <tr>
          <td>ARB</td>
          <td id="precoARB">0.0000</td>
          <td id="precoMedioARB">0.0000</td>
          <td id="quantidadeARB">0.0000</td>
          <td id="plrARB">0.00%</td>
        </tr>
        <tr>
          <td>RDNT</td>
          <td id="precoRDNT">0.0000</td>
          <td id="precoMedioRDNT">0.0000</td>
          <td id="quantidadeRDNT">0.0000</td>
          <td id="plrRDNT">0.00%</td>
        </tr>
      </tbody>
    </table>
  </main>

  <script>
    // Configuração inicial das moedas
    const moedas = {
      KAS: { id: "kassia", precoAtual: 0, precoMedio: 0, quantidade: 0 },
      ARB: { id: "arbitrum", precoAtual: 0, precoMedio: 0, quantidade: 0 },
      RDNT: { id: "rdnt", precoAtual: 0, precoMedio: 0, quantidade: 0 }
    };

    // URL da API da CoinGecko
    const apiUrl = "https://api.coingecko.com/api/v3/simple/price?ids=kassia,arbitrum,rdnt&vs_currencies=usd";

    // Atualiza preços das moedas a cada 10 segundos
    async function atualizarPrecos() {
      try {
        const response = await fetch(apiUrl);
        const data = await response.json();
        
        moedas.KAS.precoAtual = data.kassia.usd || 0;
        moedas.ARB.precoAtual = data.arbitrum.usd || 0;
        moedas.RDNT.precoAtual = data.rdnt.usd || 0;

        atualizarTabela();
      } catch (error) {
        console.error("Erro ao buscar preços:", error);
      }
    }

    // Atualiza a tabela com os dados das moedas
    function atualizarTabela() {
      for (const [key, moeda] of Object.entries(moedas)) {
        document.getElementById(`preco${key}`).textContent = moeda.precoAtual.toFixed(4);
        document.getElementById(`precoMedio${key}`).textContent = moeda.precoMedio.toFixed(4);
        document.getElementById(`quantidade${key}`).textContent = moeda.quantidade.toFixed(4);

        // Calcula o PLR (Percentual de Lucro ou Prejuízo)
        const plr = moeda.precoMedio > 0 ? ((moeda.precoAtual - moeda.precoMedio) / moeda.precoMedio) * 100 : 0;
        document.getElementById(`plr${key}`).textContent = `${plr.toFixed(2)}%`;
      }
    }

    // Adiciona um aporte
    document.getElementById("aporteForm").addEventListener("submit", (event) => {
      event.preventDefault();
      const moeda = document.getElementById("moeda").value;
      const precoCompra = parseFloat(document.getElementById("precoCompra").value);
      const quantidade = parseFloat(document.getElementById("quantidade").value);

      if (moedas[moeda]) {
        const totalInvestido = moedas[moeda].precoMedio * moedas[moeda].quantidade + precoCompra * quantidade;
        moedas[moeda].quantidade += quantidade;
        moedas[moeda].precoMedio = totalInvestido / moedas[moeda].quantidade;
        atualizarTabela();
      }
    });

    // Inicia a atualização automática
    setInterval(atualizarPrecos, 10000); // Atualiza preços a cada 10 segundos
    atualizarPrecos(); // Faz a primeira atualização imediatamente
  </script>
</body>
</html>
