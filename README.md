<html lang="en">
<head>
    <!-- ... (mantenha o head original igual) ... -->
    <!-- Remova a linha do XLSX e adicione a API do Google -->
    <script src="https://apis.google.com/js/api.js"></script>
</head>
<body>
    <!-- ... (mantenha o body original igual) ... -->
    <button type="button" onclick="handleAuthClick()">Autorizar Google Drive</button>
    <button type="button" onclick="salvarXML()">Salvar em XML no Drive</button>

    <script>
        // Adicione estas variáveis no início do script
        const CLIENT_ID = '499238518719-2k6kdlhghgick0pcsvk8oumnn7g0v2nc.apps.googleusercontent.com.apps.googleusercontent.com.apps.googleusercontent.com';
        const API_KEY = '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';
        const SCOPE = 'https://www.googleapis.com/auth/drive.file';
        
        let gapiInicializado = false;
        let usuarioAutenticado = false;

        // Funções de autenticação do Google
        function inicializarGapi() {
            gapi.load('client:auth2', () => {
                gapi.client.init({
                    apiKey: API_KEY,
                    clientId: CLIENT_ID,
                    scope: SCOPE
                }).then(() => {
                    gapiInicializado = true;
                    gapi.auth2.getAuthInstance().isSignedIn.listen(updateAuthStatus);
                    updateAuthStatus(gapi.auth2.getAuthInstance().isSignedIn.get());
                });
            });
        }

        function handleAuthClick() {
            if (!gapiInicializado) return;
            
            if (!usuarioAutenticado) {
                gapi.auth2.getAuthInstance().signIn();
            } else {
                gapi.auth2.getAuthInstance().signOut();
            }
        }

        function updateAuthStatus(isAutenticado) {
            usuarioAutenticado = isAutenticado;
            document.querySelectorAll('button')[1].disabled = !isAutenticado;
        }

        // Função para gerar o XML
        function gerarXML() {
            const nomeAcs = document.getElementById('nomeAcs').value;
            const equipe = document.getElementById('equipe').value;
            const data = document.getElementById('data').value;
            
            let xml = '<?xml version="1.0" encoding="UTF-8"?>\n<risco_familiar>\n';
            xml += `  <acs>${nomeAcs}</acs>\n`;
            xml += `  <equipe>${equipe}</equipe>\n`;
            xml += `  <data>${data}</data>\n`;
            xml += '  <moradores>\n';

            document.querySelectorAll('#tableBody tr').forEach(tr => {
                const campos = {
                    nome: tr.querySelector('.nome-morador').value,
                    cnsCpf: tr.querySelector('.cnsCpf').value,
                    escore: tr.querySelector('.escoreTotal').textContent,
                    classificacao: tr.querySelector('.classificacaoRisco').textContent
                };

                xml += '    <morador>\n';
                Object.entries(campos).forEach(([tag, valor]) => {
                    xml += `      <${tag}>${valor || ''}</${tag}>\n`;
                });
                xml += '    </morador>\n';
            });

            xml += '  </moradores>\n</risco_familiar>';
            return xml;
        }

        // Função para salvar no Drive
        async function salvarXML() {
            if (!usuarioAutenticado) {
                alert('Por favor, autorize o acesso ao Google Drive primeiro!');
                return;
            }

            const xmlContent = gerarXML();
            const filename = `Risco_Familiar_${Date.now()}.xml`;
            
            const file = new Blob([xmlContent], {type: 'application/xml'});
            const metadata = {
                name: filename,
                mimeType: 'application/xml'
            };

            const form = new FormData();
            form.append('metadata', new Blob([JSON.stringify(metadata)], {type: 'application/json'}));
            form.append('file', file);

            try {
                const resposta = await fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
                    method: 'POST',
                    headers: new Headers({'Authorization': 'Bearer ' + gapi.auth.getToken().access_token}),
                    body: form
                });
                
                const resultado = await resposta.json();
                if(resultado.error) throw resultado.error;
                alert('Arquivo salvo com sucesso no Google Drive!');
            } catch (erro) {
                console.error('Erro ao salvar:', erro);
                alert('Erro ao salvar o arquivo!');
            }
        }

        // Inicialize a API ao carregar a página
        window.onload = () => {
            adicionarLinhas();
            inicializarGapi();
        };

        // ... (mantenha as demais funções originais iguais) ...
    </script>
</body>
</html>
