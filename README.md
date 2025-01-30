// Adicione no início do script
const CLIENT_ID = 'SEU_CLIENT_ID';
const API_KEY = 'SUA_API_KEY';
const DISCOVERY_DOCS = ["https://www.googleapis.com/discovery/v1/apis/drive/v3/rest"];
const SCOPES = 'https://www.googleapis.com/auth/drive.file';

function initClient() {
    gapi.client.init({
        apiKey: API_KEY,
        clientId: CLIENT_ID,
        discoveryDocs: DISCOVERY_DOCS,
        scope: SCOPES
    }).then(() => {
        gapi.auth2.getAuthInstance().isSignedIn.listen(updateSigninStatus);
        updateSigninStatus(gapi.auth2.getAuthInstance().isSignedIn.get());
    });
}

// Mantenha as outras funções como handleAuthClick e updateSigninStatus do exemplo anterior

async function salvarPlanilha() {
    if(!gapi.auth2.getAuthInstance().isSignedIn.get()) {
        alert('Por favor, autentique-se primeiro!');
        return;
    }

    // ... (código para gerar o Excel igual ao anterior)

    const fileMetadata = {
        'name': 'Risco_Familiar.xlsx',
        'mimeType': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    };

    const form = new FormData();
    form.append('metadata', new Blob([JSON.stringify(fileMetadata)], {type: 'application/json'}));
    form.append('file', new Blob([wbout], {type: 'application/octet-stream'}));

    try {
        const response = await gapi.client.drive.files.create({
            resource: fileMetadata,
            media: {
                mimeType: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                body: new Blob([wbout], {type: 'application/octet-stream'})
            },
            fields: 'id'
        });

        if(response.status === 200) {
            alert('Arquivo salvo no Google Drive! ID: ' + response.result.id);
        }
    } catch (error) {
        console.error('Erro:', error.result.error.message);
        alert('Erro: ' + error.result.error.message);
    }
}

// Carrega a API ao iniciar
gapi.load('client:auth2', initClient);
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cálculo de Risco Familiar</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }
        table, th, td {
            border: 1px solid black;
        }
        th, td {
            padding: 10px;
            text-align: center;
        }
        .result {
            margin-top: 20px;
            font-weight: bold;
        }
        .quantity-control {
            display: flex;
            gap: 5px;
            align-items: center;
            justify-content: center;
        }
        .quantity-control button {
            padding: 2px 8px;
        }
        .quantity-control input {
            width: 50px;
            text-align: center;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
</head>
<body>
    <h1>Cálculo do Risco Familiar</h1>
    <form id="riskForm">
        <div>
            <label for="nomeAcs">NOME DO ACS:</label>
            <input type="text" id="nomeAcs">
        </div>
        <div>
            <label for="equipe">EQUIPE:</label>
            <input type="text" id="equipe">
        </div>
        <div>
            <label for="data">DATA (Dia, Mês e Ano):</label>
            <input type="date" id="data">
        </div>
        <br>
        <table>
            <thead>
                <tr>
                    <th>Nome do Morador</th>
                    <th>CNS/CPF</th>
                    <th>Acamado</th>
                    <th>Deficiência Física</th>
                    <th>Deficiência Mental</th>
                    <th>Baixas Condições de Saneamento</th>
                    <th>Desnutrição Grave</th>
                    <th>Drogadição</th>
                    <th>Desemprego</th>
                    <th>Analfabetismo</th>
                    <th>Menor de 6 meses</th>
                    <th>Maior de 70 anos</th>
                    <th>Hipertensão Arterial Sistêmica</th>
                    <th>Diabetes Mellitus</th>
                    <th>Relação Morador/Cômodo</th>
                    <th>Escore Total</th>
                    <th>Classificação do Risco Familiar</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <!-- As linhas serão adicionadas dinamicamente -->
            </tbody>
        </table>
        <button type="button" onclick="salvarPlanilha()">Salvar em Planilha</button>
    </form>
    <script>
        const tableBody = document.getElementById('tableBody');

        function adicionarLinhas() {
            for (let i = 0; i < 12; i++) {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td><input type="text" class="nome-morador"></td>
                    <td><input type="text" class="cnsCpf"></td>
                    <!-- Indicadores com botões de quantidade -->
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="2" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="2" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="1" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="1" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="1" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="1" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <div class="quantity-control">
                            <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                            <input type="number" class="risk-quantity" value="0" min="0" data-value="1" onchange="calcularLinha(this)">
                            <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                        </div>
                    </td>
                    <td>
                        <select class="relationFactor" onchange="calcularLinha(this)">
                            <option value="3">Maior que 1 (3 pontos)</option>
                            <option value="2">Igual a 1 (2 pontos)</option>
                            <option value="0">Menor que 1 (0 ponto)</option>
                        </select>
                    </td>
                    <td class="escoreTotal">0</td>
                    <td class="classificacaoRisco"></td>
                `;
                tableBody.appendChild(tr);
            }
        }

        function adjustQuantity(button, delta) {
            const input = button.parentElement.querySelector('.risk-quantity');
            let value = parseInt(input.value) + delta;
            if (value < 0) value = 0;
            input.value = value;
            input.dispatchEvent(new Event('change'));
        }

        function calcularLinha(element) {
            const row = element.closest('tr');
            const riskQuantities = row.querySelectorAll('.risk-quantity');
            const relationFactor = parseInt(row.querySelector('.relationFactor').value || 0);

            let totalScore = Array.from(riskQuantities).reduce((sum, input) => {
                const quantity = parseInt(input.value) || 0;
                const valuePerUnit = parseInt(input.dataset.value);
                return sum + (quantity * valuePerUnit);
            }, relationFactor);

            let classificacao = '';
            if (totalScore <= 6) {
                classificacao = 'R1 - Risco Menor';
            } else if (totalScore <= 8) {
                classificacao = 'R2 - Risco Médio';
            } else {
                classificacao = 'R3 - Risco Máximo';
            }

            row.querySelector('.escoreTotal').textContent = totalScore;
            row.querySelector('.classificacaoRisco').textContent = classificacao;
        }

        function salvarPlanilha() {
            const nomeAcs = document.getElementById('nomeAcs').value;
            const equipe = document.getElementById('equipe').value;
            const data = document.getElementById('data').value;
            const rows = tableBody.querySelectorAll('tr');

            const headers = [
                "NOME DO ACS", "EQUIPE", "DATA (Dia, Mês e Ano)", "Nome do Morador", "CNS/CPF", "Acamado",
                "Deficiência Física", "Deficiência Mental", "Baixas Condições de Saneamento", "Desnutrição Grave",
                "Drogadição", "Desemprego", "Analfabetismo", "Menor de 6 meses", "Maior de 70 anos",
                "Hipertensão Arterial Sistêmica", "Diabetes Mellitus", "Relação Morador/Cômodo", "Escore Total",
                "Classificação do Risco Familiar"
            ];

            const dataArray = [];
            rows.forEach((row) => {
                const nomeMorador = row.querySelector('.nome-morador').value || '';
                const cnsCpf = row.querySelector('.cnsCpf').value || '';
                const relationFactor = row.querySelector('.relationFactor').value || '';
                const riskQuantities = row.querySelectorAll('.risk-quantity');
                const indicators = Array.from(riskQuantities).map((input) => input.value);
                const escoreTotal = row.querySelector('.escoreTotal').textContent || 0;
                const classificacaoRisco = row.querySelector('.classificacaoRisco').textContent || '';

                dataArray.push([
                    nomeAcs,
                    equipe,
                    data,
                    nomeMorador,
                    cnsCpf,
                    ...indicators,
                    relationFactor,
                    escoreTotal,
                    classificacaoRisco,
                ]);
            });

            const ws = XLSX.utils.aoa_to_sheet([headers, ...dataArray]);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Risco Familiar");
            XLSX.writeFile(wb, "Risco_Familiar.xlsx");
        }

        window.onload = adicionarLinhas;
    </script>
</body>
</html>
