
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Análise de Volume</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.4/moment.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.4/locale/pt-br.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #4361ee;
            --secondary: #3f37c9;
            --success: #4cc9f0;
            --info: #4895ef;
            --warning: #f72585;
            --light: #f8f9fa;
            --dark: #212529;
            --background: #f5f7fb;
            --card-bg: #ffffff;
            --text: #333333;
            --text-light: #6c757d;
            --border: #dee2e6;
            --positive: #28a745;
            --negative: #dc3545;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--background);
            color: var(--text);
            line-height: 1.6;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }

        header {
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            color: white;
            padding: 25px 0;
            border-radius: 12px;
            margin-bottom: 30px;
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
            text-align: center;
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
        }

        .description {
            font-size: 1.1rem;
            opacity: 0.9;
            max-width: 800px;
            margin: 0 auto;
        }

        .controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: var(--card-bg);
            padding: 15px 20px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
            margin-bottom: 25px;
            flex-wrap: wrap;
            gap: 15px;
        }

        .btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn:hover {
            background: var(--secondary);
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        .btn i {
            font-size: 1rem;
        }

        .date-filter {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .date-filter select, .date-filter input {
            padding: 8px 12px;
            border-radius: 6px;
            border: 1px solid var(--border);
            background: var(--card-bg);
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .stat-card {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 25px 20px;
            text-align: center;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            border-left: 4px solid var(--primary);
        }

        .stat-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
        }

        .stat-card h3 {
            font-size: 1rem;
            color: var(--text-light);
            margin-bottom: 10px;
        }

        .stat-value {
            font-size: 2.2rem;
            font-weight: bold;
            color: var(--primary);
            margin-bottom: 5px;
        }

        .dashboard {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
            gap: 25px;
            margin-bottom: 30px;
        }

        .card {
            background: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
        }

        .card h2 {
            color: var(--secondary);
            margin-bottom: 20px;
            font-size: 1.4rem;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 12px;
            border-bottom: 2px solid var(--border);
        }

        .card h2 i {
            color: var(--primary);
        }

        .chart-container {
            height: 300px;
            margin-top: 15px;
            position: relative;
        }

        .loading {
            text-align: center;
            padding: 40px;
            font-size: 1.2rem;
            color: var(--text-light);
        }

        .loading i {
            font-size: 2rem;
            margin-bottom: 15px;
            color: var(--primary);
        }

        .error {
            background-color: #ffeaea;
            color: #dc3545;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
            border-left: 4px solid #dc3545;
        }

        .error i {
            font-size: 1.5rem;
            margin-bottom: 10px;
        }

        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        .data-table th, .data-table td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid var(--border);
        }

        .data-table th {
            background-color: var(--primary);
            color: white;
            font-weight: 600;
        }

        .data-table tr:nth-child(even) {
            background-color: rgba(0, 0, 0, 0.02);
        }

        .data-table tr:hover {
            background-color: rgba(0, 0, 0, 0.05);
        }

        footer {
            text-align: center;
            margin-top: 40px;
            padding: 20px;
            color: var(--text-light);
            font-size: 0.9rem;
            border-top: 1px solid var(--border);
        }

        .insight-card {
            background: linear-gradient(135deg, var(--info), var(--success));
            color: white;
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
        }

        .insight-card h3 {
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .status-indicator {
            display: inline-block;
            width: 10px;
            height: 10px;
            border-radius: 50%;
            margin-right: 8px;
        }

        .status-connected {
            background-color: #28a745;
        }

        .status-disconnected {
            background-color: #dc3545;
        }

        .connection-info {
            margin-top: 10px;
            font-size: 0.9rem;
            opacity: 0.8;
        }

        .file-input-container {
            position: relative;
            overflow: hidden;
            display: inline-block;
        }

        .file-input-container input[type=file] {
            position: absolute;
            left: 0;
            top: 0;
            opacity: 0;
            width: 100%;
            height: 100%;
            cursor: pointer;
        }

        .variation-positive {
            color: var(--positive);
            font-weight: bold;
        }

        .variation-negative {
            color: var(--negative);
            font-weight: bold;
        }

        .variation-neutral {
            color: var(--text-light);
        }

        @media (max-width: 768px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
            
            .controls {
                flex-direction: column;
                align-items: stretch;
            }
            
            .date-filter {
                justify-content: center;
            }
            
            h1 {
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-chart-line"></i> Dashboard de Análise de Volume</h1>
            <p class="description">
                <span id="connection-status" class="status-indicator status-connected"></span>
                Dashboard interativo - Funciona 100% offline
                <a href="https://docs.google.com/spreadsheets/d/1iLftWtEUacg6iYCzZKhChR6b5nqrNQxTz6R3lulBCro/edit?gid=1457446284#gid=1457446284"> Faça download da planilha aqui 📄</a>
                <div class="connection-info" id="connection-info">Pronto para análise</div>
            </p>
        </header>
        
        <div class="controls">
            <button id="refresh-btn" class="btn">
                <i class="fas fa-sync-alt"></i> Atualizar Dados
            </button>
            <div class="date-filter">
                <select id="month-filter">
                    <option value="">Todos os meses</option>
                    <option value="1">Janeiro</option>
                    <option value="2">Fevereiro</option>
                    <option value="3">Março</option>
                    <option value="4">Abril</option>
                    <option value="5">Maio</option>
                    <option value="6">Junho</option>
                    <option value="7">Julho</option>
                    <option value="8">Agosto</option>
                    <option value="9">Setembro</option>
                    <option value="10">Outubro</option>
                    <option value="11">Novembro</option>
                    <option value="12">Dezembro</option>
                </select>
                <select id="year-filter">
                    <option value="">Todos os anos</option>
                </select>
            </div>
            <div class="file-input-container">
                <button id="upload-btn" class="btn">
                    <i class="fas fa-upload"></i> Carregar CSV
                </button>
                <input type="file" id="file-input" accept=".csv">
            </div>
            <button id="export-btn" class="btn">
                <i class="fas fa-download"></i> Exportar Relatório
            </button>
        </div>
        
        <div id="error-message" class="error" style="display: none;">
            <i class="fas fa-exclamation-triangle"></i>
            <p id="error-text"></p>
        </div>
        
        <div id="dashboard">
            <div class="stats-grid">
                <div class="stat-card">
                    <h3>Volume Total Expedido</h3>
                    <div class="stat-value" id="total-expedido">0</div>
                </div>
                <div class="stat-card">
                    <h3>Média Diária</h3>
                    <div class="stat-value" id="media-diaria">0</div>
                </div>
                <div class="stat-card">
                    <h3>Dia com Maior Volume</h3>
                    <div class="stat-value" id="dia-maior-volume">-</div>
                </div>
                <div class="stat-card">
                    <h3>Mês com Maior Volume</h3>
                    <div class="stat-value" id="mes-maior-volume">-</div>
                </div>
            </div>
            
            <div class="dashboard">
                <div class="card">
                    <h2><i class="fas fa-chart-line"></i> Tendência de Volume por Data</h2>
                    <div class="chart-container">
                        <canvas id="volume-trend-chart"></canvas>
                    </div>
                </div>
                
                <div class="card">
                    <h2><i class="fas fa-percentage"></i> Variação Percentual Diária</h2>
                    <div class="chart-container">
                        <canvas id="variation-chart"></canvas>
                    </div>
                </div>
                
                <div class="card">
                    <h2><i class="fas fa-calendar-week"></i> Volume por Dia da Semana</h2>
                    <div class="chart-container">
                        <canvas id="weekday-volume-chart"></canvas>
                    </div>
                </div>
                
                <div class="card">
                    <h2><i class="fas fa-calendar-alt"></i> Volume por Mês</h2>
                    <div class="chart-container">
                        <canvas id="monthly-volume-chart"></canvas>
                    </div>
                </div>
                
                <div class="card">
                    <h2><i class="fas fa-chart-bar"></i> Análise de Frequência de Aumento</h2>
                    <div class="chart-container">
                        <canvas id="increase-frequency-chart"></canvas>
                    </div>
                </div>
            </div>
            
            <div class="card">
                <h2><i class="fas fa-lightbulb"></i> Insights e Tendências</h2>
                <div id="trend-summary"></div>
                
                <div class="insight-card">
                    <h3><i class="fas fa-chart-line"></i> Recomendações Baseadas nos Dados</h3>
                    <div id="recommendations"></div>
                </div>
            </div>
            
            <div class="card">
                <h2><i class="fas fa-table"></i> Dados Detalhados</h2>
                <div style="overflow-x: auto;">
                    <table class="data-table">
                        <thead>
                            <tr>
                                <th>Data</th>
                                <th>Volume Expedido</th>
                                <th>Dia da Semana</th>
                                <th>Mês</th>
                                <th>Variação</th>
                            </tr>
                        </thead>
                        <tbody id="data-table-body">
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
    
    <footer>
        <p>Dashboard de Análise de Volume. Desenvolvido por Eliane ERB. &copy; 2025 - Funciona 100% offline</p>
    </footer>

    <script>
        // Elementos da DOM
        const errorElement = document.getElementById('error-message');
        const errorTextElement = document.getElementById('error-text');
        const refreshButton = document.getElementById('refresh-btn');
        const exportButton = document.getElementById('export-btn');
        const uploadButton = document.getElementById('upload-btn');
        const fileInput = document.getElementById('file-input');
        const monthFilter = document.getElementById('month-filter');
        const yearFilter = document.getElementById('year-filter');
        const connectionStatus = document.getElementById('connection-status');
        const connectionInfo = document.getElementById('connection-info');
        
        // Dados em memória
        let allData = [];
        let filteredData = [];
        let charts = {};
        
        // Inicialização
        document.addEventListener('DOMContentLoaded', function() {
            // Configurar moment.js para português
            moment.locale('pt-br');
            
            // Carregar dados iniciais
            loadData();
            
            // Configurar eventos
            refreshButton.addEventListener('click', loadData);
            exportButton.addEventListener('click', exportReport);
            uploadButton.addEventListener('click', () => fileInput.click());
            fileInput.addEventListener('change', handleFileUpload);
            monthFilter.addEventListener('change', applyFilters);
            yearFilter.addEventListener('change', applyFilters);
        });
        
        // Função para carregar dados
        function loadData() {
            hideError();
            updateConnectionStatus(true, 'Carregando dados de exemplo...');
            
            try {
                // Gerar dados de exemplo realistas
                allData = generateRealisticSampleData();
                populateYearFilter();
                applyFilters();
                
                updateConnectionStatus(true, 'Dados carregados com sucesso');
                
            } catch (error) {
                console.error('Erro ao carregar dados:', error);
                showError('Erro ao carregar dados: ' + error.message);
            }
        }
        
        // Função para gerar dados de exemplo realistas
        function generateRealisticSampleData() {
            console.log('Gerando dados de exemplo realistas...');
            const data = [];
            const startDate = new Date('2023-01-01');
            const endDate = new Date('2023-12-31');
            const weekdays = ['Domingo', 'Segunda', 'Terça', 'Quarta', 'Quinta', 'Sexta', 'Sábado'];
            
            let currentDate = new Date(startDate);
            let previousVolume = 850;
            
            // Padrões sazonais
            const seasonalPatterns = {
                0: 0.7,  // Domingo - menor volume
                1: 1.2,  // Segunda - maior volume
                2: 1.1,  // Terça
                3: 1.0,  // Quarta
                4: 1.05, // Quinta
                5: 0.9,  // Sexta
                6: 0.8   // Sábado
            };
            
            // Variação por mês (sazonalidade)
            const monthlyPatterns = {
                0: 0.9,   // Janeiro
                1: 0.85,  // Fevereiro
                2: 0.95,  // Março
                3: 1.0,   // Abril
                4: 1.1,   // Maio
                5: 1.15,  // Junho
                6: 1.05,  // Julho
                7: 1.2,   // Agosto
                8: 1.25,  // Setembro
                9: 1.3,   // Outubro
                10: 1.35, // Novembro
                11: 1.4   // Dezembro
            };
            
            while (currentDate <= endDate) {
                const dayOfWeek = currentDate.getDay();
                const month = currentDate.getMonth();
                
                // Variação base + sazonalidade
                let baseVariation = (Math.random() - 0.3) * 80;
                let volume = Math.max(200, previousVolume + baseVariation);
                
                // Aplicar padrões sazonais
                volume *= seasonalPatterns[dayOfWeek];
                volume *= monthlyPatterns[month];
                
                previousVolume = volume;
                
                data.push({
                    date: new Date(currentDate),
                    volume: Math.round(volume),
                    days: Math.floor(Math.random() * 3) + 1,
                    weekday: weekdays[dayOfWeek],
                    dayOfWeek: dayOfWeek,
                    month: month + 1,
                    year: currentDate.getFullYear(),
                    monthYear: `${(month + 1).toString().padStart(2, '0')}/${currentDate.getFullYear()}`
                });
                
                currentDate.setDate(currentDate.getDate() + 1);
            }
            
            console.log('Dados gerados:', data.length, 'registros');
            return data;
        }
        
        // Função para processar dados de arquivo carregado
        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            hideError();
            updateConnectionStatus(false, 'Processando arquivo...');
            
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const content = e.target.result;
                    const processedData = processFileData(content, file.name);
                    
                    if (processedData && processedData.length > 0) {
                        allData = processedData;
                        populateYearFilter();
                        applyFilters();
                        updateConnectionStatus(true, 'Dados carregados do arquivo: ' + file.name);
                    } else {
                        throw new Error('Nenhum dado válido encontrado no arquivo');
                    }
                } catch (error) {
                    console.error('Erro ao processar arquivo:', error);
                    showError('Erro ao processar arquivo: ' + error.message);
                    // Recarregar dados de exemplo em caso de erro
                    loadData();
                }
            };
            
            reader.onerror = function() {
                showError('Erro ao ler o arquivo');
                loadData();
            };
            
            if (file.name.endsWith('.csv')) {
                reader.readAsText(file);
            } else {
                showError('Formato de arquivo não suportado. Use CSV.');
                loadData();
            }
        }
        
        // Função para processar dados de arquivo CSV
        function processFileData(content, fileName) {
            const weekdays = ['Domingo', 'Segunda', 'Terça', 'Quarta', 'Quinta', 'Sexta', 'Sábado'];
            const processedData = [];
            
            if (fileName.endsWith('.csv')) {
                const lines = content.split('\n').filter(line => line.trim() !== '');
                const headers = lines[0].split(',').map(h => h.trim().replace(/"/g, ''));
                
                // Encontrar índices das colunas necessárias
                const dateIndex = headers.findIndex(h => 
                    h.toLowerCase().includes('data'));
                const volumeIndex = headers.findIndex(h => 
                    h.toLowerCase().includes('qntd') || h.toLowerCase().includes('volume') || 
                    h.toLowerCase().includes('expedida') || h.toLowerCase().includes('quantidade'));
                
                if (dateIndex === -1 || volumeIndex === -1) {
                    throw new Error('Colunas "Data" e "Volume/QNTD Expedida" são obrigatórias no CSV');
                }
                
                // Processar cada linha
                for (let i = 1; i < lines.length; i++) {
                    const line = lines[i].trim();
                    if (!line) continue;
                    
                    // Lidar com CSV que pode ter vírgulas dentro de aspas
                    const values = parseCSVLine(line);
                    
                    // Tentar parsear a data
                    let date;
                    try {
                        const dateStr = values[dateIndex].replace(/"/g, '').trim();
                        
                        // Tentar diferentes formatos de data
                        if (dateStr.includes('/')) {
                            const parts = dateStr.split('/');
                            if (parts.length === 3) {
                                // Formato DD/MM/AAAA ou MM/DD/AAAA
                                if (parts[2].length === 4) {
                                    date = new Date(parts[2], parts[1] - 1, parts[0]);
                                } else if (parts[0].length === 4) {
                                    date = new Date(parts[0], parts[1] - 1, parts[2]);
                                }
                            }
                        } else if (dateStr.includes('-')) {
                            date = new Date(dateStr);
                        }
                    } catch (e) {
                        console.warn('Erro ao parsear data:', values[dateIndex], e);
                        continue;
                    }
                    
                    if (!date || isNaN(date.getTime())) {
                        console.warn('Data inválida:', values[dateIndex]);
                        continue;
                    }
                    
                    const volume = parseFloat(values[volumeIndex].replace(/"/g, '')) || 0;
                    
                    if (volume <= 0) continue;
                    
                    const month = date.getMonth() + 1;
                    const year = date.getFullYear();
                    const monthYear = `${month.toString().padStart(2, '0')}/${year}`;
                    const dayOfWeek = date.getDay();
                    
                    processedData.push({
                        date: date,
                        volume: volume,
                        days: 1,
                        weekday: weekdays[dayOfWeek],
                        dayOfWeek: dayOfWeek,
                        month: month,
                        year: year,
                        monthYear: monthYear
                    });
                }
            }
            
            console.log('Dados processados do arquivo:', processedData.length, 'registros');
            return processedData;
        }
        
        // Função auxiliar para parsear linha CSV
        function parseCSVLine(line) {
            const result = [];
            let current = '';
            let inQuotes = false;
            
            for (let i = 0; i < line.length; i++) {
                const char = line[i];
                
                if (char === '"') {
                    inQuotes = !inQuotes;
                } else if (char === ',' && !inQuotes) {
                    result.push(current);
                    current = '';
                } else {
                    current += char;
                }
            }
            
            result.push(current);
            return result;
        }
        
        // Função para atualizar status da conexão
        function updateConnectionStatus(connected, message) {
            if (connected) {
                connectionStatus.className = 'status-indicator status-connected';
            } else {
                connectionStatus.className = 'status-indicator status-disconnected';
            }
            connectionInfo.textContent = message;
        }
        
        // Função para popular o filtro de anos
        function populateYearFilter() {
            const years = [...new Set(allData.map(item => item.year))].sort();
            yearFilter.innerHTML = '<option value="">Todos os anos</option>';
            
            years.forEach(year => {
                const option = document.createElement('option');
                option.value = year;
                option.textContent = year;
                yearFilter.appendChild(option);
            });
        }
        
        // Função para aplicar filtros
        function applyFilters() {
            const selectedMonth = monthFilter.value;
            const selectedYear = yearFilter.value;
            
            filteredData = allData.filter(item => {
                let match = true;
                
                if (selectedMonth) {
                    match = match && item.month.toString() === selectedMonth;
                }
                
                if (selectedYear) {
                    match = match && item.year.toString() === selectedYear;
                }
                
                return match;
            });
            
            if (filteredData.length === 0) {
                showError('Nenhum dado encontrado com os filtros selecionados.');
            } else {
                hideError();
                updateDashboard();
            }
        }
        
        // Função para atualizar o dashboard
        function updateDashboard() {
            const analysis = analyzeData(filteredData);
            displayStats(analysis);
            createCharts(analysis);
            generateTrendSummary(analysis);
            populateDataTable(filteredData);
        }
        
        // Função para analisar os dados
        function analyzeData(data) {
            if (data.length === 0) return {};
            
            const totalVolume = data.reduce((sum, item) => sum + item.volume, 0);
            const averageDaily = totalVolume / data.length;
            
            // Agrupar por dia da semana
            const weekdayStats = {};
            data.forEach(item => {
                if (!weekdayStats[item.dayOfWeek]) {
                    weekdayStats[item.dayOfWeek] = { total: 0, count: 0 };
                }
                weekdayStats[item.dayOfWeek].total += item.volume;
                weekdayStats[item.dayOfWeek].count++;
            });
            
            // Calcular médias por dia da semana
            const weekdayAverages = {};
            Object.keys(weekdayStats).forEach(weekday => {
                weekdayAverages[weekday] = weekdayStats[weekday].total / weekdayStats[weekday].count;
            });
            
            // Agrupar por mês
            const monthlyStats = {};
            data.forEach(item => {
                if (!monthlyStats[item.monthYear]) {
                    monthlyStats[item.monthYear] = { total: 0, count: 0 };
                }
                monthlyStats[item.monthYear].total += item.volume;
                monthlyStats[item.monthYear].count++;
            });
            
            // Calcular médias por mês
            const monthlyAverages = {};
            Object.keys(monthlyStats).forEach(month => {
                monthlyAverages[month] = monthlyStats[month].total / monthlyStats[month].count;
            });
            
            // Encontrar dia com maior volume
            let maxWeekdayVolume = 0;
            let maxWeekday = null;
            Object.keys(weekdayAverages).forEach(weekday => {
                if (weekdayAverages[weekday] > maxWeekdayVolume) {
                    maxWeekdayVolume = weekdayAverages[weekday];
                    maxWeekday = parseInt(weekday);
                }
            });
            
            // Encontrar mês com maior volume
            let maxMonthlyVolume = 0;
            let maxMonth = null;
            Object.keys(monthlyAverages).forEach(month => {
                if (monthlyAverages[month] > maxMonthlyVolume) {
                    maxMonthlyVolume = monthlyAverages[month];
                    maxMonth = month;
                }
            });
            
            // Análise de aumentos
            const increaseByWeekday = {};
            let previousVolume = null;
            
            // Ordenar dados por data
            const sortedData = [...data].sort((a, b) => a.date - b.date);
            
            sortedData.forEach((item, index) => {
                if (index > 0 && previousVolume !== null) {
                    if (item.volume > previousVolume) {
                        if (!increaseByWeekday[item.dayOfWeek]) {
                            increaseByWeekday[item.dayOfWeek] = 0;
                        }
                        increaseByWeekday[item.dayOfWeek]++;
                    }
                }
                previousVolume = item.volume;
            });
            
            // Encontrar dia com maior frequência de aumento
            let maxIncreaseFrequency = 0;
            let maxIncreaseWeekday = null;
            Object.keys(increaseByWeekday).forEach(weekday => {
                if (increaseByWeekday[weekday] > maxIncreaseFrequency) {
                    maxIncreaseFrequency = increaseByWeekday[weekday];
                    maxIncreaseWeekday = parseInt(weekday);
                }
            });
            
            return {
                totalVolume,
                averageDaily,
                maxWeekday: {
                    day: maxWeekday,
                    volume: maxWeekdayVolume
                },
                maxMonth: {
                    month: maxMonth,
                    volume: maxMonthlyVolume
                },
                weekdayAverages,
                monthlyAverages,
                increaseByWeekday,
                maxIncreaseWeekday: {
                    day: maxIncreaseWeekday,
                    frequency: maxIncreaseFrequency
                },
                rawData: sortedData
            };
        }
        
        // Função para exibir estatísticas
        function displayStats(analysis) {
            document.getElementById('total-expedido').textContent = analysis.totalVolume.toLocaleString('pt-BR');
            document.getElementById('media-diaria').textContent = analysis.averageDaily.toLocaleString('pt-BR', { maximumFractionDigits: 0 });
            
            const weekdayNames = ['Domingo', 'Segunda', 'Terça', 'Quarta', 'Quinta', 'Sexta', 'Sábado'];
            document.getElementById('dia-maior-volume').textContent = 
                analysis.maxWeekday.day !== null ? 
                weekdayNames[analysis.maxWeekday.day] : '-';
                
            document.getElementById('mes-maior-volume').textContent = 
                analysis.maxMonth.month !== null ? 
                analysis.maxMonth.month : '-';
        }
        
        // Função para criar gráficos
        function createCharts(analysis) {
            const weekdayNames = ['Dom', 'Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sáb'];
            
            // Destruir gráficos existentes
            Object.values(charts).forEach(chart => {
                if (chart) chart.destroy();
            });
            
            // Gráfico de tendência de volume por data
            const trendCtx = document.getElementById('volume-trend-chart').getContext('2d');
            
            // Simplificar labels para não sobrecarregar o gráfico
            const dates = analysis.rawData.map(item => moment(item.date).format('DD/MM'));
            const volumes = analysis.rawData.map(item => item.volume);
            
            // Mostrar apenas alguns labels para não ficar poluído
            const step = Math.max(1, Math.floor(dates.length / 20));
            const displayDates = dates.filter((_, index) => index % step === 0);
            const displayVolumes = volumes.filter((_, index) => index % step === 0);
            
            charts.trend = new Chart(trendCtx, {
                type: 'line',
                data: {
                    labels: displayDates,
                    datasets: [{
                        label: 'Volume Expedido',
                        data: displayVolumes,
                        borderColor: '#4361ee',
                        backgroundColor: 'rgba(67, 97, 238, 0.1)',
                        borderWidth: 2,
                        fill: true,
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Tendência de Volume ao Longo do Tempo'
                        },
                        legend: {
                            display: true
                        }
                    },
                    scales: {
                        x: {
                            title: {
                                display: true,
                                text: 'Data'
                            }
                        },
                        y: {
                            title: {
                                display: true,
                                text: 'Volume'
                            },
                            beginAtZero: true
                        }
                    }
                }
            });
            
            // NOVO GRÁFICO: Variação Percentual Diária
            const variationCtx = document.getElementById('variation-chart').getContext('2d');
            const variationData = calculateDailyVariation(analysis.rawData);
            
            charts.variation = new Chart(variationCtx, {
                type: 'bar',
                data: {
                    labels: variationData.dates,
                    datasets: [{
                        label: 'Variação Percentual',
                        data: variationData.percentages,
                        backgroundColor: variationData.percentages.map(p => 
                            p > 0 ? 'rgba(40, 167, 69, 0.7)' : 'rgba(220, 53, 69, 0.7)'
                        ),
                        borderColor: variationData.percentages.map(p => 
                            p > 0 ? 'rgb(40, 167, 69)' : 'rgb(220, 53, 69)'
                        ),
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Variação Percentual Diária (Verde: Aumento, Vermelho: Queda)'
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `Variação: ${context.raw > 0 ? '+' : ''}${context.raw.toFixed(1)}%`;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            title: {
                                display: true,
                                text: 'Data'
                            }
                        },
                        y: {
                            title: {
                                display: true,
                                text: 'Variação (%)'
                            },
                            beginAtZero: false
                        }
                    }
                }
            });
            
            // Gráfico de volume por dia da semana
            const weekdayCtx = document.getElementById('weekday-volume-chart').getContext('2d');
            const weekdayLabels = [0,1,2,3,4,5,6].map(day => weekdayNames[day]);
            const weekdayData = [0,1,2,3,4,5,6].map(day => analysis.weekdayAverages[day] || 0);
            
            charts.weekday = new Chart(weekdayCtx, {
                type: 'bar',
                data: {
                    labels: weekdayLabels,
                    datasets: [{
                        label: 'Volume Médio',
                        data: weekdayData,
                        backgroundColor: [
                            'rgba(67, 97, 238, 0.7)',
                            'rgba(76, 201, 240, 0.7)',
                            'rgba(72, 149, 239, 0.7)',
                            'rgba(247, 37, 133, 0.7)',
                            'rgba(102, 16, 242, 0.7)',
                            'rgba(63, 55, 201, 0.7)',
                            'rgba(4, 190, 254, 0.7)'
                        ],
                        borderColor: [
                            'rgb(67, 97, 238)',
                            'rgb(76, 201, 240)',
                            'rgb(72, 149, 239)',
                            'rgb(247, 37, 133)',
                            'rgb(102, 16, 242)',
                            'rgb(63, 55, 201)',
                            'rgb(4, 190, 254)'
                        ],
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Volume Médio por Dia da Semana'
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Volume Médio'
                            }
                        }
                    }
                }
            });
            
            // Gráfico de volume por mês
            const monthlyCtx = document.getElementById('monthly-volume-chart').getContext('2d');
            const monthlyLabels = Object.keys(analysis.monthlyAverages).sort();
            const monthlyData = monthlyLabels.map(month => analysis.monthlyAverages[month]);
            
            charts.monthly = new Chart(monthlyCtx, {
                type: 'bar',
                data: {
                    labels: monthlyLabels,
                    datasets: [{
                        label: 'Volume Médio',
                        data: monthlyData,
                        backgroundColor: 'rgba(76, 201, 240, 0.7)',
                        borderColor: 'rgb(76, 201, 240)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Volume Médio por Mês'
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Volume Médio'
                            }
                        }
                    }
                }
            });
            
            // Gráfico de frequência de aumento
            const increaseCtx = document.getElementById('increase-frequency-chart').getContext('2d');
            const increaseLabels = [0,1,2,3,4,5,6].map(day => weekdayNames[day]);
            const increaseData = [0,1,2,3,4,5,6].map(day => analysis.increaseByWeekday[day] || 0);
            
            charts.increase = new Chart(increaseCtx, {
                type: 'bar',
                data: {
                    labels: increaseLabels,
                    datasets: [{
                        label: 'Frequência de Aumento',
                        data: increaseData,
                        backgroundColor: 'rgba(247, 37, 133, 0.7)',
                        borderColor: 'rgb(247, 37, 133)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Frequência de Aumento por Dia da Semana'
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Número de Aumentos'
                            }
                        }
                    }
                }
            });
        }
        
        // Função para calcular variação percentual diária
        function calculateDailyVariation(data) {
            const variations = {
                dates: [],
                percentages: []
            };
            
            for (let i = 1; i < data.length; i++) {
                const current = data[i];
                const previous = data[i-1];
                
                const variation = ((current.volume - previous.volume) / previous.volume) * 100;
                
                variations.dates.push(moment(current.date).format('DD/MM'));
                variations.percentages.push(parseFloat(variation.toFixed(1)));
            }
            
            // Simplificar para não sobrecarregar o gráfico
            const step = Math.max(1, Math.floor(variations.dates.length / 15));
            return {
                dates: variations.dates.filter((_, index) => index % step === 0),
                percentages: variations.percentages.filter((_, index) => index % step === 0)
            };
        }
        
        // Função para gerar resumo das tendências
        function generateTrendSummary(analysis) {
            const weekdayNames = ['Domingo', 'Segunda-feira', 'Terça-feira', 'Quarta-feira', 'Quinta-feira', 'Sexta-feira', 'Sábado'];
            const summaryElement = document.getElementById('trend-summary');
            const recommendationsElement = document.getElementById('recommendations');
            
            // Calcular estatísticas de variação
            const variationStats = calculateVariationStats(analysis.rawData);
            
            let summaryHTML = `
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 15px;">
                    <div>
                        <p><strong>Volume Total Expedido:</strong> ${analysis.totalVolume.toLocaleString('pt-BR')}</p>
                        <p><strong>Média Diária:</strong> ${analysis.averageDaily.toLocaleString('pt-BR', { maximumFractionDigits: 0 })}</p>
                        <p><strong>Dia com Maior Volume:</strong> ${analysis.maxWeekday.day !== null ? weekdayNames[analysis.maxWeekday.day] : '-'} (${analysis.maxWeekday.volume.toLocaleString('pt-BR', { maximumFractionDigits: 0 })})</p>
                    </div>
                    <div>
                        <p><strong>Mês com Maior Volume:</strong> ${analysis.maxMonth.month || '-'} (${analysis.maxMonth.volume.toLocaleString('pt-BR', { maximumFractionDigits: 0 })})</p>
                        <p><strong>Maior Aumento:</strong> <span class="variation-positive">+${variationStats.maxIncrease.toFixed(1)}%</span></p>
                        <p><strong>Maior Queda:</strong> <span class="variation-negative">${variationStats.maxDecrease.toFixed(1)}%</span></p>
            `;
            
            if (analysis.maxIncreaseWeekday.day !== null) {
                summaryHTML += `<p><strong>Dia com Maior Frequência de Aumento:</strong> ${weekdayNames[analysis.maxIncreaseWeekday.day]} (${analysis.maxIncreaseWeekday.frequency} aumentos)</p>`;
            }
            
            // Análise de tendência geral
            const volumes = analysis.rawData.map(item => item.volume);
            let increases = 0;
            let decreases = 0;
            
            for (let i = 1; i < volumes.length; i++) {
                if (volumes[i] > volumes[i-1]) increases++;
                else if (volumes[i] < volumes[i-1]) decreases++;
            }
            
            const totalChanges = increases + decreases;
            const increasePercentage = totalChanges > 0 ? (increases / totalChanges * 100).toFixed(1) : 0;
            
            summaryHTML += `
                        <p><strong>Tendência Geral:</strong> ${increasePercentage > 50 ? 'Crescente' : 'Decrescente'} (${increasePercentage}% de dias com aumento)</p>
                    </div>
                </div>
            `;
            
            summaryElement.innerHTML = summaryHTML;
            
            // Gerar recomendações
            let recommendationsHTML = '';
            
            if (analysis.maxWeekday.day !== null) {
                recommendationsHTML += `<p><i class="fas fa-check-circle"></i> <strong>Alocar mais recursos nas ${weekdayNames[analysis.maxWeekday.day].toLowerCase()}s</strong> para aproveitar os picos de volume.</p>`;
            }
            
            if (analysis.maxIncreaseWeekday.day !== null) {
                recommendationsHTML += `<p><i class="fas fa-check-circle"></i> <strong>Antecipar preparativos nas ${weekdayNames[analysis.maxIncreaseWeekday.day].toLowerCase()}s</strong> para lidar com os aumentos frequentes de volume.</p>`;
            }
            
            if (increasePercentage > 60) {
                recommendationsHTML += `<p><i class="fas fa-check-circle"></i> <strong>Considerar expansão de capacidade</strong> devido à tendência consistentemente crescente.</p>`;
            } else if (increasePercentage < 40) {
                recommendationsHTML += `<p><i class="fas fa-exclamation-triangle"></i> <strong>Analisar causas da tendência decrescente</strong> e considerar estratégias para reverter esta situação.</p>`;
            }
            
            // Recomendações baseadas na variação
            if (variationStats.avgVariation > 5) {
                recommendationsHTML += `<p><i class="fas fa-chart-line"></i> <strong>Variação positiva consistente</strong> - manter estratégias atuais.</p>`;
            } else if (variationStats.avgVariation < -2) {
                recommendationsHTML += `<p><i class="fas fa-exclamation-triangle"></i> <strong>Variação negativa preocupante</strong> - revisar operações.</p>`;
            }
            
            recommendationsElement.innerHTML = recommendationsHTML;
        }
        
        // Função para calcular estatísticas de variação
        function calculateVariationStats(data) {
            let maxIncrease = -Infinity;
            let maxDecrease = Infinity;
            let totalVariation = 0;
            let variationCount = 0;
            
            for (let i = 1; i < data.length; i++) {
                const current = data[i];
                const previous = data[i-1];
                
                const variation = ((current.volume - previous.volume) / previous.volume) * 100;
                
                if (variation > maxIncrease) maxIncrease = variation;
                if (variation < maxDecrease) maxDecrease = variation;
                
                totalVariation += variation;
                variationCount++;
            }
            
            return {
                maxIncrease: maxIncrease !== -Infinity ? maxIncrease : 0,
                maxDecrease: maxDecrease !== Infinity ? maxDecrease : 0,
                avgVariation: variationCount > 0 ? totalVariation / variationCount : 0
            };
        }
        
        // Função para popular a tabela de dados
        function populateDataTable(data) {
            const tableBody = document.getElementById('data-table-body');
            tableBody.innerHTML = '';
            
            // Ordenar por data
            const sortedData = [...data].sort((a, b) => b.date - a.date);
            
            // Mostrar apenas os 50 registros mais recentes
            const displayData = sortedData.slice(0, 50);
            
            displayData.forEach((item, index) => {
                const row = document.createElement('tr');
                
                // Encontrar variação em relação ao dia anterior
                let variation = '-';
                let variationClass = 'variation-neutral';
                
                if (index < sortedData.length - 1) {
                    const prevItem = sortedData[index + 1];
                    const change = ((item.volume - prevItem.volume) / prevItem.volume * 100);
                    variation = `${change > 0 ? '+' : ''}${change.toFixed(1)}%`;
                    
                    // Definir classe baseada na variação
                    if (change > 0) {
                        variationClass = 'variation-positive';
                    } else if (change < 0) {
                        variationClass = 'variation-negative';
                    }
                }
                
                row.innerHTML = `
                    <td>${moment(item.date).format('DD/MM/YYYY')}</td>
                    <td>${item.volume.toLocaleString('pt-BR')}</td>
                    <td>${item.weekday}</td>
                    <td>${item.monthYear}</td>
                    <td class="${variationClass}">${variation}</td>
                `;
                
                tableBody.appendChild(row);
            });
            
            // Adicionar mensagem se houver mais dados
            if (sortedData.length > 50) {
                const infoRow = document.createElement('tr');
                infoRow.innerHTML = `
                    <td colspan="5" style="text-align: center; background-color: #f8f9fa; font-style: italic;">
                        Mostrando os 50 registros mais recentes de ${sortedData.length} no total
                    </td>
                `;
                tableBody.appendChild(infoRow);
            }
        }
        
        function exportReport() {
            // Criar um objeto com os dados para exportação
            const exportData = {
                dashboard: {
                    totalVolume: document.getElementById('total-expedido').textContent,
                    averageDaily: document.getElementById('media-diaria').textContent,
                    maxWeekday: document.getElementById('dia-maior-volume').textContent,
                    maxMonth: document.getElementById('mes-maior-volume').textContent
                },
                rawData: filteredData.map(item => ({
                    data: moment(item.date).format('DD/MM/YYYY'),
                    volume: item.volume,
                    diaSemana: item.weekday,
                    mesAno: item.monthYear
                }))
            };
            
            // Criar um blob com os dados
            const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: 'application/json' });
            
            // Criar um link para download
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `relatorio-volume-${moment().format('YYYY-MM-DD')}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            
            alert('Relatório exportado com sucesso!');
        }
        
        // Funções auxiliares de UI
        function showError(message) {
            errorTextElement.textContent = message;
            errorElement.style.display = 'block';
        }
        
        function hideError() {
            errorElement.style.display = 'none';
        }
    </script>
</body>
</html>
