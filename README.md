<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Controle de Comissões</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background: #f2f2f2;
        margin: 0; padding: 20px;
    }
    h2 { color: #333; }
    .container {
        background: #fff;
        padding: 20px;
        border-radius: 8px;
        max-width: 800px;
        margin: auto;
        box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    input, select, button {
        padding: 8px;
        margin: 5px 0;
        width: 100%;
        box-sizing: border-box;
    }
    button {
        background: #007bff;
        color: white;
        border: none;
        cursor: pointer;
        border-radius: 4px;
    }
    button:hover { background: #0056b3; }

    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
    }
    table, th, td {
        border: 1px solid #ccc;
    }
    th, td {
        padding: 10px;
        text-align: center;
    }
    .total {
        text-align: right;
        padding: 10px;
        font-size: 18px;
        font-weight: bold;
    }
</style>
</head>
<body>

<div class="container">
    <h2>Cadastro de Vendedor</h2>
    <input type="text" id="nomeVendedor" placeholder="Nome do vendedor">
    <button onclick="adicionarVendedor()">Adicionar Vendedor</button>

    <h2>Registrar Comissão</h2>
    <select id="vendedorSelect"></select>
    <input type="number" id="valor" placeholder="Valor da comissão (R$)">
    <input type="date" id="data">
    <input type="text" id="meta" placeholder="Meta de vendas atingida">
    <button onclick="adicionarComissao()">Registrar Comissão</button>

    <h2>Comissões Registradas</h2>
    <table>
        <thead>
            <tr>
                <th>Vendedor</th>
                <th>Valor (R$)</th>
                <th>Data</th>
                <th>Meta Atingida</th>
                <th>Ação</th>
            </tr>
        </thead>
        <tbody id="tabelaComissoes"></tbody>
    </table>

    <div class="total" id="totalPago">Total Pago: R$ 0,00</div>
</div>

<script>
    let vendedores = JSON.parse(localStorage.getItem("vendedores")) || [];
    let comissoes = JSON.parse(localStorage.getItem("comissoes")) || [];

    function salvarDados() {
        localStorage.setItem("vendedores", JSON.stringify(vendedores));
        localStorage.setItem("comissoes", JSON.stringify(comissoes));
    }

    function carregarVendedores() {
        let select = document.getElementById("vendedorSelect");
        select.innerHTML = "";
        vendedores.forEach(v => {
            let option = document.createElement("option");
            option.value = v;
            option.textContent = v;
            select.appendChild(option);
        });
    }

    function adicionarVendedor() {
        let nome = document.getElementById("nomeVendedor").value;
        if (nome.trim() === "") {
            alert("Digite um nome válido!");
            return;
        }
        vendedores.push(nome);
        salvarDados();
        carregarVendedores();
        document.getElementById("nomeVendedor").value = "";
    }

    function adicionarComissao() {
        let vendedor = document.getElementById("vendedorSelect").value;
        let valor = parseFloat(document.getElementById("valor").value);
        let data = document.getElementById("data").value;
        let meta = document.getElementById("meta").value;

        if (!vendedor || !valor || !data) {
            alert("Preencha todos os campos obrigatórios!");
            return;
        }

        comissoes.push({ vendedor, valor, data, meta });
        salvarDados();
        carregarTabela();

        document.getElementById("valor").value = "";
        document.getElementById("data").value = "";
        document.getElementById("meta").value = "";
    }

    function removerComissao(index) {
        comissoes.splice(index, 1);
        salvarDados();
        carregarTabela();
    }

    function carregarTabela() {
        let tbody = document.getElementById("tabelaComissoes");
        tbody.innerHTML = "";

        let total = 0;

        comissoes.forEach((c, index) => {
            total += c.valor;
            let tr = document.createElement("tr");
            tr.innerHTML = `
                <td>${c.vendedor}</td>
                <td>R$ ${c.valor.toFixed(2)}</td>
                <td>${c.data}</td>
                <td>${c.meta || "-"}</td>
                <td><button style="background:red" onclick="removerComissao(${index})">Excluir</button></td>
            `;
            tbody.appendChild(tr);
        });

        document.getElementById("totalPago").textContent = 
            "Total Pago: R$ " + total.toFixed(2);
    }

    // Inicialização
    carregarVendedores();
    carregarTabela();
</script>

</body>
</html>
