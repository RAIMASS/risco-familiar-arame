```html
<!-- Adicione no <head> -->
<script src="https://apis.google.com/js/api.js"></script>

<!-- Adicione um botão de login -->
<button onclick="handleAuth()" id="loginButton" style="display: none;">Login com Google</button>

<script>
  // Configuração do cliente Google API
  function initClient() {
    gapi.client.init({
      apiKey: 'SUA_API_KEY',
      clientId: 'SEU_CLIENT_ID.apps.googleusercontent.com',
      discoveryDocs: ['https://www.googleapis.com/discovery/v1/apis/drive/v3/rest'],
      scope: 'https://www.googleapis.com/auth/drive.file'
    }).then(() => {
      document.getElementById('loginButton').style.display = 'block';
    });
  }

  // Função de login
  function handleAuth() {
    gapi.auth2.getAuthInstance().signIn().then(() => {
      salvarPlanilha(); // Chama a função após o login
    });
  }

  // Carrega a API ao iniciar
  gapi.load('client:auth2', initClient);

  // Função modificada para salvar no Google Drive
  async function salvarPlanilha() {
    const accessToken = gapi.auth.getToken().access_token;

    // Gera o arquivo XLSX (código existente)
    const nomeAcs = document.getElementById('nomeAcs').value;
    const equipe = document.getElementById('equipe').value;
    const data = document.getElementById('data').value;
    const rows = tableBody.querySelectorAll('tr');

    // ... (código existente para gerar 'wb') ...

    // Converte para Blob
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
</script>
```
