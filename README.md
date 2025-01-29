<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script>
// Configurações
const API_URL = 'SUA_URL_DE_API_SEGURA'; // Usar serviço backend como proxy
const FILE_NAME = `dados_${new Date().toISOString().slice(0,10)}.xlsx`;

async function saveToCloud() {
    try {
        // 1. Coletar dados dinamicamente (exemplo)
        const data = await fetchDados();
        
        // 2. Criar planilha
        const ws = XLSX.utils.aoa_to_sheet(data);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Dados");

        // 3. Converter para arquivo
        const wbout = XLSX.write(wb, {type: 'array', bookType: 'xlsx'});
        
        // 4. Preparar envio
        const formData = new FormData();
        formData.append('file', new Blob([wbout], {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'}), FILE_NAME);

        // 5. Enviar para API
        const response = await fetch(API_URL, {
            method: 'POST',
            headers: {
                'Authorization': 'Bearer SEU_TOKEN' // Implementar OAuth2
            },
            body: formData
        });
        
        handleResponse(response);
        
    } catch (error) {
        showError(error);
    }
}

// Funções auxiliares
async function fetchDados() {
    // Implementar busca de dados real (API, formulário, etc.)
    return [
        ["Nome", "Idade", "Cidade"],
        ["João", 25, "São Paulo"],
        ["Maria", 30, "Rio de Janeiro"]
    ];
}

function handleResponse(response) {
    if(response.ok) {
        showSuccess('Arquivo salvo na nuvem com sucesso!');
    } else {
        throw new Error(`Erro ${response.status}: ${response.statusText}`);
    }
}

function showSuccess(message) {
    const alertDiv = document.createElement('div');
    alertDiv.className = 'alert success';
    alertDiv.textContent = message;
    document.body.appendChild(alertDiv);
    setTimeout(() => alertDiv.remove(), 3000);
}

function showError(error) {
    console.error('Erro:', error);
    const alertDiv = document.createElement('div');
    alertDiv.className = 'alert error';
    alertDiv.textContent = `Falha: ${error.message}`;
    document.body.appendChild(alertDiv);
}
</script>

<style>
    /* Estilos melhorados */
    body {
        font-family: 'Segoe UI', Arial, sans-serif;
        padding: 2rem;
        max-width: 800px;
        margin: 0 auto;
    }
    
    button {
        padding: 12px 24px;
        background-color: #4285f4;
        color: white;
        border: none;
        border-radius: 6px;
        cursor: pointer;
        transition: all 0.3s;
        font-size: 16px;
    }
    
    button:hover {
        background-color: #357abd;
        transform: translateY(-2px);
        box-shadow: 0 3px 6px rgba(0,0,0,0.1);
    }
    
    .alert {
        padding: 15px;
        margin: 20px 0;
        border-radius: 4px;
    }
    
    .success {
        background-color: #d4edda;
        color: #155724;
        border: 1px solid #c3e6cb;
    }
    
    .error {
        background-color: #f8d7da;
        color: #721c24;
        border: 1px solid #f5c6cb;
    }
</style>

<button onclick="saveToCloud()">Exportar para Nuvem</button>
