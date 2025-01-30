<!-- ... (Manter o head e o início do body igual à versão anterior) ... -->

<table>
    <thead>
        <tr>
            <th>Nome do Morador</th>
            <th>CNS/CPF</th>
            <!-- Restaurar os indicadores -->
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

<script>
// Função corrigida para adicionar linhas com todos os indicadores
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
            
            <!-- Repetir o mesmo padrão para os demais indicadores -->
            <td>
                <div class="quantity-control">
                    <button type="button" onclick="adjustQuantity(this, -1)">-</button>
                    <input type="number" class="risk-quantity" value="0" min="0" data-value="3" onchange="calcularLinha(this)">
                    <button type="button" onclick="adjustQuantity(this, 1)">+</button>
                </div>
            </td>
            
            <!-- Continuar com os demais indicadores... -->
            
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

// ... (Manter o restante do código da versão anterior) ...
</script>
