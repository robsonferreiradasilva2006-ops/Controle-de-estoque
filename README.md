from IPython.display import display, HTML

html_content = """
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Controle de Almoxarifado</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body class="bg-gradient-to-br from-blue-50 to-indigo-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <!-- Header -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
            <h1 class="text-3xl font-bold text-gray-800 text-center">
                📦 Sistema de Controle de Almoxarifado
            </h1>
            <p class="text-gray-600 text-center mt-2">Gerencie seu estoque de forma eficiente</p>
        </div>

        <!-- Navigation Tabs -->
        <div class="bg-white rounded-xl shadow-lg mb-8">
            <div class="flex border-b">
                <button onclick="showTab('estoque')" id="tab-estoque" class="flex-1 py-4 px-6 text-center font-medium text-blue-600 border-b-2 border-blue-600 bg-blue-50">
                    📊 Estoque Atual
                </button>
                <button onclick="showTab('cadastro')" id="tab-cadastro" class="flex-1 py-4 px-6 text-center font-medium text-gray-600 hover:text-blue-600 hover:bg-gray-50">
                    ➕ Cadastrar Item
                </button>
                <button onclick="showTab('movimentacao')" id="tab-movimentacao" class="flex-1 py-4 px-6 text-center font-medium text-gray-600 hover:text-blue-600 hover:bg-gray-50">
                    🔄 Movimentações
                </button>
                <button onclick="showTab('historico')" id="tab-historico" class="flex-1 py-4 px-6 text-center font-medium text-gray-600 hover:text-blue-600 hover:bg-gray-50">
                    📋 Histórico
                </button>
            </div>
        </div>

        <!-- Estoque Atual Tab -->
        <div id="content-estoque" class="tab-content">
            <div class="bg-white rounded-xl shadow-lg p-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-semibold text-gray-800">Estoque Atual</h2>
                    <div class="flex gap-2">
                        <input type="text" id="search-estoque" placeholder="Buscar item..." class="px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                        <button onclick="filterLowStock()" class="px-4 py-2 bg-orange-500 text-white rounded-lg hover:bg-orange-600 transition-colors">
                            ⚠️ Estoque Baixo
                        </button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead>
                            <tr class="bg-gray-50">
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Item</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Categoria</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Quantidade</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Estoque Mín.</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                            </tr>
                        </thead>
                        <tbody id="estoque-table" class="bg-white divide-y divide-gray-200">
                            <!-- Items will be populated here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Cadastro Tab -->
        <div id="content-cadastro" class="tab-content hidden">
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h2 class="text-2xl font-semibold text-gray-800 mb-6">Cadastrar Novo Item</h2>

                <form id="cadastro-form" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Nome do Item</label>
                        <input type="text" id="nome-item" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Categoria</label>
                        <select id="categoria-item" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">Selecione uma categoria</option>
                            <option value="Escritório">Escritório</option>
                            <option value="Limpeza">Limpeza</option>
                            <option value="Informática">Informática</option>
                            <option value="Manutenção">Manutenção</option>
                            <option value="Outros">Outros</option>
                        </select>
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Quantidade Inicial</label>
                        <input type="number" id="quantidade-inicial" min="0" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Estoque Mínimo</label>
                        <input type="number" id="estoque-minimo" min="0" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>

                    <div class="md:col-span-2">
                        <label class="block text-sm font-medium text-gray-700 mb-2">Descrição (Opcional)</label>
                        <textarea id="descricao-item" rows="3" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"></textarea>
                    </div>

                    <div class="md:col-span-2">
                        <button type="submit" class="w-full bg-blue-600 text-white py-3 px-6 rounded-lg hover:bg-blue-700 transition-colors font-medium">
                            ✅ Cadastrar Item
                        </button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Movimentação Tab -->
        <div id="content-movimentacao" class="tab-content hidden">
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h2 class="text-2xl font-semibold text-gray-800 mb-6">Registrar Movimentação</h2>

                <form id="movimentacao-form" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Selecionar Item</label>
                        <select id="item-movimentacao" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">Selecione um item</option>
                        </select>
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Tipo de Movimentação</label>
                        <select id="tipo-movimentacao" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">Selecione o tipo</option>
                            <option value="entrada">📥 Entrada</option>
                            <option value="saida">📤 Saída</option>
                        </select>
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Quantidade</label>
                        <input type="number" id="quantidade-movimentacao" min="1" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Data</label>
                        <input type="date" id="data-movimentacao" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>

                    <div class="md:col-span-2">
                        <label class="block text-sm font-medium text-gray-700 mb-2">Observações</label>
                        <textarea id="observacoes-movimentacao" rows="3" placeholder="Motivo da movimentação, fornecedor, etc." class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"></textarea>
                    </div>

                    <div class="md:col-span-2">
                        <button type="submit" class="w-full bg-green-600 text-white py-3 px-6 rounded-lg hover:bg-green-700 transition-colors font-medium">
                            🔄 Registrar Movimentação
                        </button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Histórico Tab -->
        <div id="content-historico" class="tab-content hidden">
            <div class="bg-white rounded-xl shadow-lg p-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-semibold text-gray-800">Histórico de Movimentações</h2>
                    <div class="flex gap-2">
                        <select id="filter-tipo" class="px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">Todos os tipos</option>
                            <option value="entrada">Entradas</option>
                            <option value="saida">Saídas</option>
                        </select>
                        <input type="date" id="filter-data" class="px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead>
                            <tr class="bg-gray-50">
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Data</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Item</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Tipo</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Quantidade</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Observações</th>
                            </tr>
                        </thead>
                        <tbody id="historico-table" class="bg-white divide-y divide-gray-200">
                            <!-- History will be populated here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Data storage
        let estoque = JSON.parse(localStorage.getItem('almoxarifado-estoque')) || [];
        let historico = JSON.parse(localStorage.getItem('almoxarifado-historico')) || [];

        // Initialize with sample data if empty
        if (estoque.length === 0) {
            estoque = [
                { id: 1, nome: 'Papel A4', categoria: 'Escritório', quantidade: 50, estoqueMinimo: 10, descricao: 'Papel sulfite branco' },
                { id: 2, nome: 'Caneta Azul', categoria: 'Escritório', quantidade: 25, estoqueMinimo: 5, descricao: 'Caneta esferográfica azul' },
                { id: 3, nome: 'Detergente', categoria: 'Limpeza', quantidade: 8, estoqueMinimo: 15, descricao: 'Detergente neutro 500ml' }
            ];
            saveData();
        }

        // Tab management
        function showTab(tabName) {
            // Hide all tabs
            document.querySelectorAll('.tab-content').forEach(tab => tab.classList.add('hidden'));
            document.querySelectorAll('[id^="tab-"]').forEach(btn => {
                btn.classList.remove('text-blue-600', 'border-blue-600', 'bg-blue-50');
                btn.classList.add('text-gray-600');
            });

            // Show selected tab
            document.getElementById(`content-${tabName}`).classList.remove('hidden');
            document.getElementById(`tab-${tabName}`).classList.add('text-blue-600', 'border-blue-600', 'bg-blue-50');
            document.getElementById(`tab-${tabName}`).classList.remove('text-gray-600');

            // Update content based on tab
            if (tabName === 'estoque') {
                renderEstoque();
            } else if (tabName === 'movimentacao') {
                updateItemSelect();
            } else if (tabName === 'historico') {
                renderHistorico();
            }
        }

        // Save data to localStorage
        function saveData() {
            localStorage.setItem('almoxarifado-estoque', JSON.stringify(estoque));
            localStorage.setItem('almoxarifado-historico', JSON.stringify(historico));
        }

        // Render estoque table
        function renderEstoque(filteredItems = null) {
            const items = filteredItems || estoque;
            const tbody = document.getElementById('estoque-table');

            tbody.innerHTML = items.map(item => {
                const status = item.quantidade <= item.estoqueMinimo ?
                    '<span class="px-2 py-1 text-xs font-semibold rounded-full bg-red-100 text-red-800">⚠️ Baixo</span>' :
                    '<span class="px-2 py-1 text-xs font-semibold rounded-full bg-green-100 text-green-800">✅ Normal</span>';

                return `
                    <tr class="hover:bg-gray-50">
                        <td class="px-6 py-4 whitespace-nowrap">
                            <div class="font-medium text-gray-900">${item.nome}</div>
                            <div class="text-sm text-gray-500">${item.descricao || ''}</div>
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${item.categoria}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${item.quantidade}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${item.estoqueMinimo}</td>
                        <td class="px-6 py-4 whitespace-nowrap">${status}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                            <button onclick="editItem(${item.id})" class="text-blue-600 hover:text-blue-900 mr-3">✏️ Editar</button>
                            <button onclick="deleteItem(${item.id})" class="text-red-600 hover:text-red-900">🗑️ Excluir</button>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        // Filter low stock items
        function filterLowStock() {
            const lowStockItems = estoque.filter(item => item.quantidade <= item.estoqueMinimo);
            renderEstoque(lowStockItems);
        }

        // Search functionality
        document.getElementById('search-estoque').addEventListener('input', function(e) {
            const searchTerm = e.target.value.toLowerCase();
            const filteredItems = estoque.filter(item =>
                item.nome.toLowerCase().includes(searchTerm) ||
                item.categoria.toLowerCase().includes(searchTerm)
            );
            renderEstoque(filteredItems);
        });

        // Cadastro form
        document.getElementById('cadastro-form').addEventListener('submit', function(e) {
            e.preventDefault();

            const newItem = {
                id: Date.now(),
                nome: document.getElementById('nome-item').value,
                categoria: document.getElementById('categoria-item').value,
                quantidade: parseInt(document.getElementById('quantidade-inicial').value),
                estoqueMinimo: parseInt(document.getElementById('estoque-minimo').value),
                descricao: document.getElementById('descricao-item').value
            };

            estoque.push(newItem);
            saveData();

            // Add to history
            historico.unshift({
                id: Date.now(),
                itemId: newItem.id,
                itemNome: newItem.nome,
                tipo: 'entrada',
                quantidade: newItem.quantidade,
                data: new Date().toISOString().split('T')[0],
                observacoes: 'Cadastro inicial do item'
            });
            saveData();

            alert('✅ Item cadastrado com sucesso!');
            this.reset();
            showTab('estoque');
        });

        // Update item select for movimentacao
        function updateItemSelect() {
            const select = document.getElementById('item-movimentacao');
            select.innerHTML = '<option value="">Selecione um item</option>' +
                estoque.map(item => `<option value="${item.id}">${item.nome} (Estoque: ${item.quantidade})</option>`).join('');
        }

        // Set today's date as default
        document.getElementById('data-movimentacao').value = new Date().toISOString().split('T')[0];

        // Movimentacao form
        document.getElementById('movimentacao-form').addEventListener('submit', function(e) {
            e.preventDefault();

            const itemId = parseInt(document.getElementById('item-movimentacao').value);
            const tipo = document.getElementById('tipo-movimentacao').value;
            const quantidade = parseInt(document.getElementById('quantidade-movimentacao').value);
            const data = document.getElementById('data-movimentacao').value;
            const observacoes = document.getElementById('observacoes-movimentacao').value;

            const item = estoque.find(i => i.id === itemId);

            if (tipo === 'saida' && quantidade > item.quantidade) {
                alert('❌ Quantidade insuficiente em estoque!');
                return;
            }

            // Update stock
            if (tipo === 'entrada') {
                item.quantidade += quantidade;
            } else {
                item.quantidade -= quantidade;
            }

            // Add to history
            historico.unshift({
                id: Date.now(),
                itemId: itemId,
                itemNome: item.nome,
                tipo: tipo,
                quantidade: quantidade,
                data: data,
                observacoes: observacoes
            });

            saveData();
            alert('✅ Movimentação registrada com sucesso!');
            this.reset();
            document.getElementById('data-movimentacao').value = new Date().toISOString().split('T')[0];
            updateItemSelect();
        });

        // Render historico
        function renderHistorico(filteredHistory = null) {
            const history = filteredHistory || historico;
            const tbody = document.getElementById('historico-table');

            tbody.innerHTML = history.map(mov => {
                const tipoIcon = mov.tipo === 'entrada' ? '📥' : '📤';
                const tipoClass = mov.tipo === 'entrada' ? 'text-green-600' : 'text-red-600';

                return `
                    <tr class="hover:bg-gray-50">
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${new Date(mov.data).toLocaleDateString('pt-BR')}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${mov.itemNome}</td>
                        <td class="px-6 py-4 whitespace-nowrap">
                            <span class="px-2 py-1 text-xs font-semibold rounded-full ${mov.tipo === 'entrada' ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'}">
                                ${tipoIcon} ${mov.tipo.charAt(0).toUpperCase() + mov.tipo.slice(1)}
                            </span>
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium ${tipoClass}">${mov.quantidade}</td>
                        <td class="px-6 py-4 text-sm text-gray-900">${mov.observacoes || '-'}</td>
                    </tr>
                `;
            }).join('');
        }

        // History filters
        document.getElementById('filter-tipo').addEventListener('change', filterHistory);
        document.getElementById('filter-data').addEventListener('change', filterHistory);

        function filterHistory() {
            const tipoFilter = document.getElementById('filter-tipo').value;
            const dataFilter = document.getElementById('filter-data').value;

            let filtered = historico;

            if (tipoFilter) {
                filtered = filtered.filter(mov => mov.tipo === tipoFilter);
            }

            if (dataFilter) {
                filtered = filtered.filter(mov => mov.data === dataFilter);
            }

            renderHistorico(filtered);
        }

        // Edit and delete functions
        function editItem(id) {
            const item = estoque.find(i => i.id === id);
            const newQuantity = prompt(`Editar quantidade de "${item.nome}":\nQuantidade atual: ${item.quantidade}`, item.quantidade);

            if (newQuantity !== null && !isNaN(newQuantity) && newQuantity >= 0) {
                const oldQuantity = item.quantidade;
                item.quantidade = parseInt(newQuantity);

                // Add to history
                const diff = item.quantidade - oldQuantity;
                if (diff !== 0) {
                    historico.unshift({
                        id: Date.now(),
                        itemId: item.id,
                        itemNome: item.nome,
                        tipo: diff > 0 ? 'entrada' : 'saida',
                        quantidade: Math.abs(diff),
                        data: new Date().toISOString().split('T')[0],
                        observacoes: 'Ajuste manual de estoque'
                    });
                }

                saveData();
                renderEstoque();
                alert('✅ Item atualizado com sucesso!');
            }
        }

        function deleteItem(id) {
            const item = estoque.find(i => i.id === id);
            if (confirm(`Tem certeza que deseja excluir "${item.nome}"?`)) {
                estoque = estoque.filter(i => i.id !== id);
                historico = historico.filter(h => h.itemId !== id);
                saveData();
                renderEstoque();
                alert('✅ Item excluído com sucesso!');
            }
        }

        // Initialize
        showTab('estoque');
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'96befb6913aef5ce',t:'MTc1NDY1NTc3Ni4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
"""
