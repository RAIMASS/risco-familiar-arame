<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cálculo de Risco Familiar</title>
    <style>
        /* (Seu CSS original permanece igual) */
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
    <!-- Adição do script da API do Google -->
    <script src="https://apis.google.com/js/api.js"></script>
</head>
<body>
    <h1>Cálculo do Risco Familiar</h1>
    <!-- Botão de Login Adicionado -->
    <button onclick="handleAuth()" id="loginButton" style="display: none;">Login com Google</button>
    
    <form id="riskForm">
        <!-- (Seus campos de input originais permanecem iguais) -->
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
            <!-- (Sua tabela original permanece igual) -->
            <thead>
                <tr>
                    <th>Nome do Morador</th>
                    <th>CNS/CPF</th>
                    <th>Acamado</th>
                    <th>Deficiência Física</th>
                    <!-- ... (outros cabeçalhos) ... -->
                </tr>
            </thead>
            <tbody id="tableBody"></tbody>
        </table>
        <button type="button" onclick="salvarPlanilha()">Salvar em Planilha</button>
    </form>

    <script>
        // Configuração Inicial da API do Google
        function initClient() {
            gapi.client.init({
                apiKey: 'SUA_API_KEY_AQUI',
                clientId: 'SEU_CLIENT_ID_AQUI.apps.googleusercontent.com',
                discoveryDocs: ['https://www.googleapis.com/discovery/v1/apis/drive/v3/rest'],
                scope: 'https://www.googleapis.com/auth/drive.file'
            }).then(() => {
                document.getElementById('loginButton').style.display = 'block';
            });
        }

        // Função de Login
        function handleAuth() {
            gapi.auth2.getAuthInstance().signIn().then(() => {
                salvarPlanilha();
            });
        }

        // Carregamento da API
        gapi.load('client:auth2', initClient);

        // Função modificada para salvar no Google Drive
        async function salvarPlanilha() {
            const accessToken = gapi.auth.getToken().access_token;
            
            // Geração do XLSX (código original mantido)
            const nomeAcs = document.getElementById('nomeAcs').value;
            const equipe = document.getElementById('equipe').value;
            const data = document.getElementById('data').value;
            const rows = tableBody.querySelectorAll('tr');

            const headers = [/*... (seus headers originais) ...*/];
            
            // ... (código original de geração da planilha) ...

            const ws = XLSX.utils.aoa_to_sheet([headers, ...dataArray]);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Risco Familiar");
            
            // Conversão para Blob
            const wbout = XLSX.write(wb, { bookType: 'xlsx', type: 'array' });
            const blob = new Blob([wbout], { type: 'application/octet-stream' });

            // Upload para o Google Drive
            const metadata = {
                name: 'Risco_Familiar.xlsx',
                mimeType: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
            };

            const form = new FormData();
            form.append('metadata', new Blob([JSON.stringify(metadata)], { type: 'application/json' }));
            form.append('file', blob);

            try {
                const response = await fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
                    method: 'POST',
                    headers: new Headers({ 'Authorization': 'Bearer ' + accessToken }),
                    body: form
                });
                const result = await response.json();
                alert('Arquivo salvo no Google Drive: ' + result.name);
            } catch (error) {
                console.error('Erro:', error);
                alert('Falha ao salvar no Drive.');
            }
        }

        // (Seu código JavaScript original permanece abaixo)
        const tableBody = document.getElementById('tableBody');
        
        function adicionarLinhas() { /*...*/ }
        function adjustQuantity(button, delta) { /*...*/ }
        function calcularLinha(element) { /*...*/ }

        window.onload = adicionarLinhas;
    </script>
</body>
</html>
