<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Salvar em Excel na Nuvem</title>
    <!-- Adicionei biblioteca SheetJS para Excel mais robusto -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
</head>
<body>
    <button onclick="saveToCloud()">Salvar na Nuvem</button>

    <script>
    async function saveToCloud() {
        // Dados de exemplo (personalizáveis)
        const data = [
            ["Nome", "Idade", "Cidade"],
            ["João", 25, "São Paulo"],
            ["Maria", 30, "Rio de Janeiro"]
        ];

        // Criar planilha usando SheetJS
        const ws = XLSX.utils.aoa_to_sheet(data);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Dados");

        // Converter para arquivo binário
        const wbout = XLSX.write(wb, {type: 'array', bookType: 'xlsx'});
        
        // Upload para o Google Drive usando API
        const formData = new FormData();
        formData.append('file', new Blob([wbout], {type: 'application/octet-stream'}), 'dados.xlsx');

        try {
            const response = await fetch('SUA_URL_DE_API_AQUI', {
                method: 'POST',
                body: formData
            });
            
            if(response.ok) {
                alert('Arquivo salvo na nuvem com sucesso!');
            } else {
                alert('Erro ao salvar arquivo');
            }
        } catch (error) {
            console.error('Erro:', error);
            alert('Falha na conexão');
        }
    }
    </script>

    <!-- Melhorias adicionais -->
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
        }
        button {
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</body>
</html>
