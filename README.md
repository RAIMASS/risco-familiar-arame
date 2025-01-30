<!-- Adicione esta tag no head -->
<script src="https://apis.google.com/js/api.js"></script>

<script>
// Adicione estas constantes no início do script
const CLIENT_ID = 'SEU_CLIENT_ID.apps.googleusercontent.com';
const API_KEY = 'SUA_API_KEY';
const DISCOVERY_DOCS = ["https://www.googleapis.com/discovery/v1/apis/drive/v3/rest"];
const SCOPES = 'https://www.googleapis.com/auth/drive.file';

// Modifique a função salvarPlanilha
async function salvarPlanilha() {
    // ... (código existente para coletar dados até a criação do dataArray)

    // Gerar o arquivo Excel como Blob
    const ws = XLSX.utils.aoa_to_sheet([headers, ...dataArray]);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Risco Familiar");
    const wbout = XLSX.write(wb, { bookType: 'xlsx', type: 'array' });
    const blob = new Blob([wbout], { type: 'application/octet-stream' });

    await initGoogleDrive();
    await uploadFileToDrive(blob);
}

// Adicione estas novas funções
async function initGoogleDrive() {
    return new Promise((resolve) => {
        gapi.load('client:auth2', async () => {
            await gapi.client.init({
                apiKey: API_KEY,
                clientId: CLIENT_ID,
                discoveryDocs: DISCOVERY_DOCS,
                scope: SCOPES
            });
            resolve();
        });
    });
}

async function uploadFileToDrive(blob) {
    if (!gapi.auth2.getAuthInstance().isSignedIn.get()) {
        await gapi.auth2.getAuthInstance().signIn();
    }

    const fileMetadata = {
        name: `Risco_Familiar_${Date.now()}.xlsx`,
        mimeType: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    };

    const form = new FormData();
    form.append('metadata', new Blob([JSON.stringify(fileMetadata)], { type: 'application/json' }));
    form.append('file', blob);

    const response = await fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
        method: 'POST',
        headers: new Headers({
            'Authorization': 'Bearer ' + gapi.auth.getToken().access_token
        }),
        body: form
    });

    if (response.ok) {
        alert('Arquivo salvo no Google Drive com sucesso!');
    } else {
        throw new Error('Erro ao enviar arquivo');
    }
}
</script>
