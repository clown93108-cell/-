# -
網路平台架設
<!DOCTYPE html>
<html lang="zh-TW" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>極簡智能 ERP 資源管理系統</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Chart.js for data visualization -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'Microsoft JhengHei', 'sans-serif'],
                    },
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            500: '#3b82f6',
                            600: '#2563eb',
                            700: '#1d4ed8',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        /* Custom scrollbar for better visual quality */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="h-full text-slate-800 antialiased flex flex-col md:flex-row font-sans">

    <!-- Sidebar / Navigation -->
    <aside class="w-full md:w-64 bg-slate-900 text-slate-300 flex-shrink-0 border-r border-slate-800">
        <div class="p-5 flex items-center justify-between md:justify-start space-x-3 border-b border-slate-800">
            <div class="bg-blue-600 text-white p-2 rounded-xl shadow-lg shadow-blue-500/30">
                <i data-lucide="boxes" class="w-6 h-6"></i>
            </div>
            <div>
                <h1 class="font-bold text-white text-lg leading-tight">ApexERP</h1>
                <p class="text-xs text-slate-400">企業毛利與庫存管理</p>
            </div>
        </div>

        <nav class="p-4 space-y-1">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all bg-blue-600 text-white shadow-md shadow-blue-600/20">
                <i data-lucide="line-chart" class="w-5 h-5"></i>
                <span>財務與毛利分析</span>
            </button>
            <button onclick="switchTab('products')" id="nav-products" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:text-white hover:bg-slate-800">
                <i data-lucide="package" class="w-5 h-5"></i>
                <span>商品目錄與成本</span>
                <span id="badge-product-count" class="ml-auto bg-slate-800 text-slate-300 text-xs px-2 py-0.5 rounded-full">0</span>
            </button>
            <button onclick="switchTab('sales')" id="nav-sales" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:text-white hover:bg-slate-800">
                <i data-lucide="shopping-cart" class="w-5 h-5"></i>
                <span>銷售開單與紀錄</span>
                <span id="badge-sales-count" class="ml-auto bg-slate-800 text-slate-300 text-xs px-2 py-0.5 rounded-full">0</span>
            </button>
            <button onclick="switchTab('settings')" id="nav-settings" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:text-white hover:bg-slate-800">
                <i data-lucide="database" class="w-5 h-5"></i>
                <span>資料備份與重置</span>
            </button>
        </nav>

        <div class="mt-auto p-4 border-t border-slate-800 text-xs text-slate-500 hidden md:block">
            <div class="flex items-center space-x-2 mb-1">
                <span class="w-2 h-2 rounded-full bg-emerald-500 inline-block animate-pulse"></span>
                <span class="text-slate-400 font-medium">LocalStorage 已啟用</span>
            </div>
            <p>數據自動同步至本機儲存空間</p>
        </div>
    </aside>

    <!-- Main Content Area -->
    <main class="flex-1 flex flex-col min-w-0 overflow-hidden bg-slate-50">
        <!-- Top Header Bar -->
        <header class="bg-white border-b border-slate-200 px-6 py-4 flex flex-col sm:flex-row sm:items-center justify-between gap-4">
            <div>
                <h2 id="page-title" class="text-xl font-bold text-slate-800">財務與毛利分析</h2>
                <p id="page-subtitle" class="text-xs text-slate-500">依年份與月份精準統計銷售數據與毛利指標</p>
            </div>
            
            <div class="flex items-center space-x-3">
                <button onclick="openProductModal()" class="inline-flex items-center space-x-2 bg-blue-600 hover:bg-blue-700 text-white text-sm font-medium px-4 py-2 rounded-xl shadow-sm transition-all">
                    <i data-lucide="plus" class="w-4 h-4"></i>
                    <span>新增商品</span>
                </button>
                <button onclick="openSalesModal()" class="inline-flex items-center space-x-2 bg-emerald-600 hover:bg-emerald-700 text-white text-sm font-medium px-4 py-2 rounded-xl shadow-sm transition-all">
                    <i data-lucide="receipt" class="w-4 h-4"></i>
                    <span>新增銷售記帳</span>
                </button>
            </div>
        </header>

        <!-- Main Body Scrollable Section -->
        <div class="flex-1 overflow-y-auto p-4 sm:p-6 space-y-6">

            <!-- TAB 1: Dashboard / Profit Analytics -->
            <section id="tab-dashboard" class="space-y-6">
                <!-- Date Filter Bar -->
                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-wrap items-center justify-between gap-4">
                    <div class="flex items-center space-x-3">
                        <div class="bg-slate-100 p-2 rounded-lg text-slate-600">
                            <i data-lucide="calendar" class="w-5 h-5"></i>
                        </div>
                        <span class="text-sm font-semibold text-slate-700">選擇統計區間：</span>
                    </div>

                    <div class="flex items-center space-x-3">
                        <select id="filter-year" onchange="renderDashboard()" class="bg-slate-50 border border-slate-300 text-slate-700 text-sm rounded-xl focus:ring-blue-500 focus:border-blue-500 p-2.5 font-medium">
                            <!-- Populated dynamically -->
                        </select>
                        <select id="filter-month" onchange="renderDashboard()" class="bg-slate-50 border border-slate-300 text-slate-700 text-sm rounded-xl focus:ring-blue-500 focus:border-blue-500 p-2.5 font-medium">
                            <option value="all">全年總計 (1-12月)</option>
                            <option value="01">01 月</option>
                            <option value="02">02 月</option>
                            <option value="03">03 月</option>
                            <option value="04">04 月</option>
                            <option value="05">05 月</option>
                            <option value="06">06 月</option>
                            <option value="07">07 月</option>
                            <option value="08">08 月</option>
                            <option value="09">09 月</option>
                            <option value="10">10 月</option>
                            <option value="11">11 月</option>
                            <option value="12">12 月</option>
                        </select>
                    </div>
                </div>

                <!-- KPI Metric Cards Grid -->
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                    <!-- Revenue Card -->
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm relative overflow-hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">當月總營業額</p>
                                <h3 id="stat-revenue" class="text-2xl font-bold text-slate-900 mt-2">$0</h3>
                            </div>
                            <div class="p-3 bg-blue-50 text-blue-600 rounded-xl">
                                <i data-lucide="dollar-sign" class="w-6 h-6"></i>
                            </div>
                        </div>
                        <p class="text-xs text-slate-500 mt-3 flex items-center">
                            <span id="stat-sales-qty-count" class="font-medium text-slate-700">0</span> 個賣出品項
                        </p>
                    </div>

                    <!-- Total Cost Card -->
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm relative overflow-hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">當月總銷貨成本</p>
                                <h3 id="stat-cost" class="text-2xl font-bold text-slate-900 mt-2">$0</h3>
                            </div>
                            <div class="p-3 bg-amber-50 text-amber-600 rounded-xl">
                                <i data-lucide="shopping-bag" class="w-6 h-6"></i>
                            </div>
                        </div>
                        <p class="text-xs text-slate-500 mt-3">包含所有賣出商品之原始進價成本</p>
                    </div>

                    <!-- Gross Profit Card -->
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm relative overflow-hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">當月毛利額</p>
                                <h3 id="stat-profit" class="text-2xl font-bold text-emerald-600 mt-2">$0</h3>
                            </div>
                            <div class="p-3 bg-emerald-50 text-emerald-600 rounded-xl">
                                <i data-lucide="trending-up" class="w-6 h-6"></i>
                            </div>
                        </div>
                        <p class="text-xs text-slate-500 mt-3">營業額扣除進貨成本後淨額</p>
                    </div>

                    <!-- Gross Margin Percentage Card -->
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm relative overflow-hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">當月毛利率</p>
                                <h3 id="stat-margin" class="text-2xl font-bold text-indigo-600 mt-2">0%</h3>
                            </div>
                            <div class="p-3 bg-indigo-50 text-indigo-600 rounded-xl">
                                <i data-lucide="percent" class="w-6 h-6"></i>
                            </div>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2 mt-4 overflow-hidden">
                            <div id="stat-margin-bar" class="bg-indigo-600 h-2 rounded-full transition-all duration-500" style="width: 0%"></div>
                        </div>
                    </div>
                </div>

                <!-- Monthly Trend Chart -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-sm">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="font-bold text-slate-800 text-base flex items-center space-x-2">
                            <i data-lucide="bar-chart-2" class="w-5 h-5 text-blue-600"></i>
                            <span>年度月度營收與毛利趨勢圖</span>
                        </h3>
                    </div>
                    <div class="h-64 relative">
                        <canvas id="profitChart"></canvas>
                    </div>
                </div>

                <!-- Monthly Orders Summary Table -->
                <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
                    <div class="p-5 border-b border-slate-100 flex items-center justify-between">
                        <h3 class="font-bold text-slate-800 text-base">該區間銷售紀錄明細</h3>
                        <span id="filtered-sales-badge" class="text-xs bg-slate-100 text-slate-600 px-3 py-1 rounded-full font-medium">共 0 筆記錄</span>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-xs uppercase text-slate-400 font-semibold border-b border-slate-100">
                                <tr>
                                    <th class="px-6 py-3.5">銷售日期</th>
                                    <th class="px-6 py-3.5">商品名稱</th>
                                    <th class="px-6 py-3.5 text-center">數量</th>
                                    <th class="px-6 py-3.5 text-right">銷售單價</th>
                                    <th class="px-6 py-3.5 text-right">成本單價</th>
                                    <th class="px-6 py-3.5 text-right">總銷售額</th>
                                    <th class="px-6 py-3.5 text-right">總成本</th>
                                    <th class="px-6 py-3.5 text-right">獲取毛利</th>
                                </tr>
                            </thead>
                            <tbody id="dashboard-sales-tbody" class="divide-y divide-slate-100">
                                <!-- Populated dynamically -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 2: Product Catalog & Cost Management -->
            <section id="tab-products" class="space-y-6 hidden">
                <!-- Search and Controls Header -->
                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col sm:flex-row items-center justify-between gap-4">
                    <div class="relative w-full sm:w-80">
                        <i data-lucide="search" class="w-4 h-4 absolute left-3 top-1/2 transform -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="product-search" oninput="renderProducts()" placeholder="搜尋商品名稱或編號..." class="w-full pl-9 pr-4 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                    </div>

                    <div class="flex items-center space-x-3 w-full sm:w-auto justify-end">
                        <span class="text-xs text-slate-500 bg-amber-50 text-amber-700 px-3 py-1.5 rounded-lg border border-amber-200/60 flex items-center space-x-1">
                            <i data-lucide="info" class="w-3.5 h-3.5"></i>
                            <span>提示：點擊列表表格中的藍色字體欄位可直接在線上進行修改</span>
                        </span>
                    </div>
                </div>

                <!-- Products Table -->
                <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-xs uppercase text-slate-400 font-semibold border-b border-slate-100">
                                <tr>
                                    <th class="px-6 py-3.5">商品編號</th>
                                    <th class="px-6 py-3.5">商品名稱 <span class="text-blue-500 lowercase">(可點擊編輯)</span></th>
                                    <th class="px-6 py-3.5 text-right">售價 ($) <span class="text-blue-500 lowercase">(可編輯)</span></th>
                                    <th class="px-6 py-3.5 text-right">成本 ($) <span class="text-blue-500 lowercase">(可編輯)</span></th>
                                    <th class="px-6 py-3.5 text-right">單件毛利</th>
                                    <th class="px-6 py-3.5 text-right">預估毛利率</th>
                                    <th class="px-6 py-3.5 text-center">目前庫存</th>
                                    <th class="px-6 py-3.5 text-center">操作</th>
                                </tr>
                            </thead>
                            <tbody id="products-tbody" class="divide-y divide-slate-100">
                                <!-- Dynamic Rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 3: Sales & Orders -->
            <section id="tab-sales" class="space-y-6 hidden">
                <div class="flex justify-between items-center bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm">
                    <div>
                        <h3 class="font-bold text-slate-800">銷售記帳列表</h3>
                        <p class="text-xs text-slate-500">記錄所有賣出的商品金額與成本時間點快照</p>
                    </div>
                    <button onclick="openSalesModal()" class="bg-emerald-600 hover:bg-emerald-700 text-white text-sm font-medium px-4 py-2 rounded-xl flex items-center space-x-2">
                        <i data-lucide="plus-circle" class="w-4 h-4"></i>
                        <span>快速開單</span>
                    </button>
                </div>

                <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-xs uppercase text-slate-400 font-semibold border-b border-slate-100">
                                <tr>
                                    <th class="px-6 py-3.5">銷售日期</th>
                                    <th class="px-6 py-3.5">商品名稱</th>
                                    <th class="px-6 py-3.5 text-center">售出數量</th>
                                    <th class="px-6 py-3.5 text-right">成交單價</th>
                                    <th class="px-6 py-3.5 text-right">單位成本</th>
                                    <th class="px-6 py-3.5 text-right">總金額</th>
                                    <th class="px-6 py-3.5 text-right">毛利</th>
                                    <th class="px-6 py-3.5 text-center">操作</th>
                                </tr>
                            </thead>
                            <tbody id="sales-tbody" class="divide-y divide-slate-100">
                                <!-- Dynamic Rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 4: Data Management & Settings -->
            <section id="tab-settings" class="space-y-6 hidden">
                <div class="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-sm max-w-2xl">
                    <h3 class="font-bold text-slate-800 text-lg mb-2 flex items-center space-x-2">
                        <i data-lucide="hard-drive" class="w-5 h-5 text-blue-600"></i>
                        <span>系統資料庫管理</span>
                    </h3>
                    <p class="text-sm text-slate-500 mb-6">目前的資料安全存儲於您瀏覽器的 LocalStorage 中。您隨時可以進行備份與還原。</p>

                    <div class="space-y-4">
                        <div class="p-4 bg-slate-50 rounded-xl border border-slate-200 flex items-center justify-between">
                            <div>
                                <h4 class="font-semibold text-slate-700 text-sm">匯出 JSON 資料備份</h4>
                                <p class="text-xs text-slate-500">將所有商品庫存與銷售記帳記錄匯出備份檔</p>
                            </div>
                            <button onclick="exportData()" class="px-4 py-2 bg-slate-800 hover:bg-slate-900 text-white rounded-xl text-sm font-medium flex items-center space-x-2">
                                <i data-lucide="download" class="w-4 h-4"></i>
                                <span>下載備份檔</span>
                            </button>
                        </div>

                        <div class="p-4 bg-slate-50 rounded-xl border border-slate-200 flex items-center justify-between">
                            <div>
                                <h4 class="font-semibold text-slate-700 text-sm">匯入 JSON 備份檔</h4>
                                <p class="text-xs text-slate-500">上傳先前備份的 JSON 檔案還原資料</p>
                            </div>
                            <label class="px-4 py-2 bg-slate-800 hover:bg-slate-900 text-white rounded-xl text-sm font-medium cursor-pointer flex items-center space-x-2">
                                <i data-lucide="upload" class="w-4 h-4"></i>
                                <span>選擇備份檔</span>
                                <input type="file" id="import-file" accept=".json" onchange="importData(event)" class="hidden">
                            </label>
                        </div>

                        <div class="p-4 bg-rose-50 rounded-xl border border-rose-200 flex items-center justify-between">
                            <div>
                                <h4 class="font-semibold text-rose-800 text-sm">重置整個 ERP 系統</h4>
                                <p class="text-xs text-rose-600">將商品庫存與銷售歷史完全清空（回復初始乾淨狀態）</p>
                            </div>
                            <button onclick="confirmResetAll()" class="px-4 py-2 bg-rose-600 hover:bg-rose-700 text-white rounded-xl text-sm font-medium flex items-center space-x-2">
                                <i data-lucide="trash-2" class="w-4 h-4"></i>
                                <span>清空所有資料</span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>
        </div>
    </main>

    <!-- MODAL 1: Add/Edit Product Modal -->
    <div id="modal-product" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl shadow-xl w-full max-w-md overflow-hidden transform transition-all">
            <div class="px-6 py-4 bg-slate-900 text-white flex justify-between items-center">
                <h3 id="modal-product-title" class="font-bold text-base">新增商品</h3>
                <button onclick="closeProductModal()" class="text-slate-400 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="form-product" onsubmit="handleProductSubmit(event)" class="p-6 space-y-4">
                <input type="hidden" id="prod-id">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">商品編號 / SKU</label>
                    <input type="text" id="prod-sku" placeholder="例如: PROD-001" required class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">商品名稱</label>
                    <input type="text" id="prod-name" placeholder="請輸入商品完整名稱" required class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">售價 ($)</label>
                        <input type="number" id="prod-price" min="0" step="1" placeholder="0" required oninput="calcModalMargin()" class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">進貨成本 ($)</label>
                        <input type="number" id="prod-cost" min="0" step="1" placeholder="0" required oninput="calcModalMargin()" class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                    </div>
                </div>

                <!-- Live Margin Preview Box -->
                <div class="p-3 bg-blue-50 rounded-xl border border-blue-100 flex justify-between text-xs text-blue-900">
                    <span>預估毛利額: <strong id="modal-preview-profit">$0</strong></span>
                    <span>預估毛利率: <strong id="modal-preview-margin">0%</strong></span>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">初始庫存量</label>
                    <input type="number" id="prod-stock" min="0" step="1" placeholder="0" required class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-blue-500 focus:border-blue-500">
                </div>

                <div class="pt-2 flex justify-end space-x-3">
                    <button type="button" onclick="closeProductModal()" class="px-4 py-2 bg-slate-100 text-slate-600 hover:bg-slate-200 text-sm font-medium rounded-xl">取消</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white text-sm font-medium rounded-xl shadow-sm">儲存商品</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL 2: Add Sales Record Modal -->
    <div id="modal-sales" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl shadow-xl w-full max-w-md overflow-hidden transform transition-all">
            <div class="px-6 py-4 bg-emerald-700 text-white flex justify-between items-center">
                <h3 class="font-bold text-base">新增銷售記帳</h3>
                <button onclick="closeSalesModal()" class="text-emerald-200 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="form-sales" onsubmit="handleSalesSubmit(event)" class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">選擇商品</label>
                    <select id="sales-product-select" onchange="onSelectSalesProduct()" required class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-emerald-500 focus:border-emerald-500">
                        <!-- Populated dynamically -->
                    </select>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">銷售數量</label>
                        <input type="number" id="sales-qty" min="1" value="1" required oninput="calcSalesModalTotals()" class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-emerald-500 focus:border-emerald-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">成交單價 ($)</label>
                        <input type="number" id="sales-price" min="0" step="1" required oninput="calcSalesModalTotals()" class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-emerald-500 focus:border-emerald-500">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">單位進貨成本 ($)</label>
                        <input type="number" id="sales-cost" min="0" step="1" required readonly class="w-full px-3 py-2 bg-slate-100 border border-slate-300 text-slate-500 rounded-xl text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">銷售日期</label>
                        <input type="date" id="sales-date" required class="w-full px-3 py-2 bg-slate-50 border border-slate-300 rounded-xl text-sm focus:ring-emerald-500 focus:border-emerald-500">
                    </div>
                </div>

                <div class="p-3 bg-emerald-50 rounded-xl border border-emerald-100 space-y-1 text-xs text-emerald-900">
                    <div class="flex justify-between">
                        <span>總銷貨收入: <strong id="sales-modal-total-price">$0</strong></span>
                        <span>總進貨成本: <strong id="sales-modal-total-cost">$0</strong></span>
                    </div>
                    <div class="flex justify-between font-semibold pt-1 border-t border-emerald-200/60">
                        <span>預估本單毛利: <strong id="sales-modal-profit">$0</strong></span>
                        <span>毛利率: <strong id="sales-modal-margin">0%</strong></span>
                    </div>
                </div>

                <div class="pt-2 flex justify-end space-x-3">
                    <button type="button" onclick="closeSalesModal()" class="px-4 py-2 bg-slate-100 text-slate-600 hover:bg-slate-200 text-sm font-medium rounded-xl">取消</button>
                    <button type="submit" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white text-sm font-medium rounded-xl shadow-sm">完成記帳</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Custom Modal Message Dialog (Replaces native alert/confirm) -->
    <div id="modal-confirm" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl shadow-xl w-full max-w-sm overflow-hidden p-6 text-center space-y-4">
            <div id="confirm-icon-bg" class="w-12 h-12 bg-rose-100 text-rose-600 rounded-full flex items-center justify-center mx-auto">
                <i data-lucide="alert-triangle" class="w-6 h-6"></i>
            </div>
            <div>
                <h3 id="confirm-title" class="font-bold text-slate-800 text-base">確定執行操作？</h3>
                <p id="confirm-desc" class="text-xs text-slate-500 mt-1">此動作無法撤銷，請確認是否繼續。</p>
            </div>
            <div class="flex justify-center space-x-3">
                <button id="btn-confirm-cancel" onclick="closeConfirmModal()" class="px-4 py-2 bg-slate-100 text-slate-600 hover:bg-slate-200 text-xs font-medium rounded-xl">取消</button>
                <button id="btn-confirm-action" class="px-4 py-2 bg-rose-600 hover:bg-rose-700 text-white text-xs font-medium rounded-xl shadow-sm">確定</button>
            </div>
        </div>
    </div>

    <!-- Notification Toast -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl z-50 flex items-center space-x-3 transition-all duration-300 opacity-0 translate-y-4 pointer-events-none">
        <i id="toast-icon" data-lucide="check-circle" class="w-5 h-5 text-emerald-400"></i>
        <span id="toast-msg" class="text-sm font-medium">操作成功</span>
    </div>

    <script>
        // Key for LocalStorage
        const STORAGE_KEY = 'apex_erp_system_data_v1';

        // Global Application State
        let appState = {
            products: [], // { id, sku, name, price, cost, stock, createdAt }
            sales: []     // { id, productId, productName, qty, price, cost, totalPrice, totalCost, profit, date, createdAt }
        };

        let chartInstance = null;
        let confirmActionHandler = null;

        // --- Core Storage Functions ---
        function loadData() {
            try {
                const raw = localStorage.getItem(STORAGE_KEY);
                if (raw) {
                    appState = JSON.parse(raw);
                } else {
                    // Start completely clean as requested (Rule 5)
                    appState = { products: [], sales: [] };
                    saveData();
                }
            } catch (e) {
                console.error("Failed to load local storage:", e);
                appState = { products: [], sales: [] };
            }
        }

        function saveData() {
            try {
                localStorage.setItem(STORAGE_KEY, JSON.stringify(appState));
            } catch (e) {
                showToast("儲存失敗：儲存空間不足", "error");
            }
            updateBadges();
        }

        function updateBadges() {
            document.getElementById('badge-product-count').innerText = appState.products.length;
            document.getElementById('badge-sales-count').innerText = appState.sales.length;
        }

        // --- UI Navigation Tabs ---
        function switchTab(tabName) {
            const tabs = ['dashboard', 'products', 'sales', 'settings'];
            tabs.forEach(t => {
                const el = document.getElementById(`tab-${t}`);
                const btn = document.getElementById(`nav-${t}`);
                if (t === tabName) {
                    el.classList.remove('hidden');
                    btn.className = "w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all bg-blue-600 text-white shadow-md shadow-blue-600/20";
                } else {
                    el.classList.add('hidden');
                    btn.className = "w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:text-white hover:bg-slate-800";
                }
            });

            // Page Titles
            const titles = {
                'dashboard': ['財務與毛利分析', '依年份與月份精準統計銷售數據與毛利指標'],
                'products': ['商品目錄與成本管理', '即時修改商品名稱、售價與成本，管理存貨數量'],
                'sales': ['銷售記帳與訂單', '記錄銷售成交訂單，系統自動扣減庫存與統計利潤'],
                'settings': ['資料管理與備份', '匯出/匯入系統備份檔或重置資料庫']
            };
            document.getElementById('page-title').innerText = titles[tabName][0];
            document.getElementById('page-subtitle').innerText = titles[tabName][1];

            if (tabName === 'dashboard') renderDashboard();
            if (tabName === 'products') renderProducts();
            if (tabName === 'sales') renderSales();
        }

        // --- Dashboard & Profit Analytics ---
        function initYearSelector() {
            const yearSelect = document.getElementById('filter-year');
            const currentYear = new Date().getFullYear();
            yearSelect.innerHTML = '';
            
            // Extract existing years from sales or default to current year
            const years = new Set([currentYear]);
            appState.sales.forEach(s => {
                if (s.date) {
                    years.add(new Date(s.date).getFullYear());
                }
            });

            Array.from(years).sort((a, b) => b - a).forEach(y => {
                const opt = document.createElement('option');
                opt.value = y;
                opt.innerText = `${y} 年`;
                yearSelect.appendChild(opt);
            });
            yearSelect.value = currentYear;
        }

        function renderDashboard() {
            const yearSelect = document.getElementById('filter-year');
            const monthSelect = document.getElementById('filter-month');

            if (!yearSelect.value) initYearSelector();

            const selectedYear = yearSelect.value;
            const selectedMonth = monthSelect.value;

            // Filter Sales Records by Selected Year and Month
            const filteredSales = appState.sales.filter(s => {
                if (!s.date) return false;
                const d = new Date(s.date);
                const matchYear = d.getFullYear().toString() === selectedYear;
                if (!matchYear) return false;

                if (selectedMonth === 'all') return true;
                const m = (d.getMonth() + 1).toString().padStart(2, '0');
                return m === selectedMonth;
            });

            // Calculate Metrics
            let totalRevenue = 0;
            let totalCost = 0;
            let totalQty = 0;

            filteredSales.forEach(s => {
                totalRevenue += (s.totalPrice || 0);
                totalCost += (s.totalCost || 0);
                totalQty += (s.qty || 0);
            });

            const grossProfit = totalRevenue - totalCost;
            const marginRatio = totalRevenue > 0 ? ((grossProfit / totalRevenue) * 100).toFixed(1) : "0.0";

            // Update Metric Cards
            document.getElementById('stat-revenue').innerText = `$${totalRevenue.toLocaleString()}`;
            document.getElementById('stat-cost').innerText = `$${totalCost.toLocaleString()}`;
            document.getElementById('stat-profit').innerText = `$${grossProfit.toLocaleString()}`;
            document.getElementById('stat-margin').innerText = `${marginRatio}%`;
            document.getElementById('stat-sales-qty-count').innerText = totalQty.toLocaleString();
            document.getElementById('stat-margin-bar').style.width = `${Math.min(Math.max(parseFloat(marginRatio), 0), 100)}%`;

            // Render Summary Sales Table
            const tbody = document.getElementById('dashboard-sales-tbody');
            document.getElementById('filtered-sales-badge').innerText = `共 ${filteredSales.length} 筆記錄`;

            if (filteredSales.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="8" class="text-center py-10 text-slate-400 text-sm">
                            <i data-lucide="folder-open" class="w-8 h-8 mx-auto mb-2 opacity-50"></i>
                            選定期間內尚無銷售與記帳數據
                        </td>
                    </tr>
                `;
            } else {
                tbody.innerHTML = filteredSales.map(s => {
                    const profit = s.totalPrice - s.totalCost;
                    return `
                        <tr class="hover:bg-slate-50 transition-colors">
                            <td class="px-6 py-4 font-mono text-xs text-slate-500">${s.date}</td>
                            <td class="px-6 py-4 font-semibold text-slate-800">${escapeHtml(s.productName)}</td>
                            <td class="px-6 py-4 text-center">${s.qty}</td>
                            <td class="px-6 py-4 text-right font-mono">$${s.price.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono">$${s.cost.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono font-semibold text-slate-800">$${s.totalPrice.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono text-slate-500">$${s.totalCost.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono font-bold ${profit >= 0 ? 'text-emerald-600' : 'text-rose-600'}">
                                $${profit.toLocaleString()}
                            </td>
                        </tr>
                    `;
                }).join('');
            }

            renderChart(selectedYear);
            lucide.createIcons();
        }

        function renderChart(year) {
            const ctx = document.getElementById('profitChart').getContext('2d');
            
            // Calculate monthly totals for the chosen year (1-12 months)
            const monthlyRevenue = Array(12).fill(0);
            const monthlyProfit = Array(12).fill(0);

            appState.sales.forEach(s => {
                if (!s.date) return;
                const d = new Date(s.date);
                if (d.getFullYear().toString() === year) {
                    const monthIndex = d.getMonth(); // 0 - 11
                    monthlyRevenue[monthIndex] += (s.totalPrice || 0);
                    monthlyProfit[monthIndex] += (s.totalPrice - s.totalCost);
                }
            });

            const labels = ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'];

            if (chartInstance) {
                chartInstance.destroy();
            }

            chartInstance = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: '毛利額 ($)',
                            data: monthlyProfit,
                            backgroundColor: 'rgba(16, 185, 129, 0.85)',
                            borderRadius: 6,
                        },
                        {
                            label: '營業額 ($)',
                            data: monthlyRevenue,
                            backgroundColor: 'rgba(59, 130, 246, 0.3)',
                            borderRadius: 6,
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'top',
                            labels: { font: { family: 'Inter', size: 12 } }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            grid: { color: '#f1f5f9' },
                            ticks: {
                                callback: function(value) { return '$' + value.toLocaleString(); }
                            }
                        },
                        x: {
                            grid: { display: false }
                        }
                    }
                }
            });
        }

        // --- Product Management (Requirement 1 & 2) ---
        function renderProducts() {
            const tbody = document.getElementById('products-tbody');
            const keyword = document.getElementById('product-search').value.toLowerCase().trim();

            const filtered = appState.products.filter(p => 
                p.name.toLowerCase().includes(keyword) || p.sku.toLowerCase().includes(keyword)
            );

            if (filtered.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="8" class="text-center py-12 text-slate-400">
                            <i data-lucide="package-x" class="w-10 h-10 mx-auto mb-2 opacity-40"></i>
                            <p class="text-base font-medium text-slate-600">目前尚無任何商品</p>
                            <p class="text-xs text-slate-400 mt-1">請點擊上方「新增商品」按鈕建立第一筆數據</p>
                        </td>
                    </tr>
                `;
            } else {
                tbody.innerHTML = filtered.map(p => {
                    const profit = p.price - p.cost;
                    const margin = p.price > 0 ? ((profit / p.price) * 100).toFixed(1) : "0.0";

                    return `
                        <tr class="hover:bg-slate-50 transition-colors">
                            <td class="px-6 py-4 font-mono text-xs font-semibold text-slate-500">${escapeHtml(p.sku)}</td>
                            
                            <!-- Inline Editable Field: Name -->
                            <td class="px-6 py-4">
                                <span onclick="makeInlineEditable(this, '${p.id}', 'name')" class="font-semibold text-blue-600 hover:text-blue-800 hover:underline cursor-pointer flex items-center space-x-1 group" title="點擊直接線上修改名稱">
                                    <span>${escapeHtml(p.name)}</span>
                                    <i data-lucide="edit-2" class="w-3 h-3 opacity-0 group-hover:opacity-100 transition-opacity"></i>
                                </span>
                            </td>

                            <!-- Inline Editable Field: Price -->
                            <td class="px-6 py-4 text-right">
                                <span onclick="makeInlineEditable(this, '${p.id}', 'price', 'number')" class="font-mono text-blue-600 hover:text-blue-800 hover:underline cursor-pointer font-medium" title="點擊直接修改售價">
                                    $${p.price.toLocaleString()}
                                </span>
                            </td>

                            <!-- Inline Editable Field: Cost -->
                            <td class="px-6 py-4 text-right">
                                <span onclick="makeInlineEditable(this, '${p.id}', 'cost', 'number')" class="font-mono text-blue-600 hover:text-blue-800 hover:underline cursor-pointer font-medium" title="點擊直接修改成本">
                                    $${p.cost.toLocaleString()}
                                </span>
                            </td>

                            <td class="px-6 py-4 text-right font-mono font-semibold ${profit >= 0 ? 'text-emerald-600' : 'text-rose-600'}">
                                $${profit.toLocaleString()}
                            </td>
                            <td class="px-6 py-4 text-right font-mono font-medium">
                                <span class="px-2 py-0.5 rounded-md text-xs ${parseFloat(margin) >= 30 ? 'bg-emerald-50 text-emerald-700' : 'bg-slate-100 text-slate-600'}">
                                    ${margin}%
                                </span>
                            </td>

                            <!-- Inline Editable Field: Stock -->
                            <td class="px-6 py-4 text-center">
                                <span onclick="makeInlineEditable(this, '${p.id}', 'stock', 'number')" class="font-mono font-bold cursor-pointer hover:underline text-slate-800" title="點擊修改庫存">
                                    ${p.stock}
                                </span>
                            </td>

                            <td class="px-6 py-4 text-center space-x-2">
                                <button onclick="editProduct('${p.id}')" class="p-1.5 text-slate-500 hover:text-blue-600 rounded-lg hover:bg-slate-100" title="完整編輯">
                                    <i data-lucide="sliders" class="w-4 h-4"></i>
                                </button>
                                <button onclick="confirmDeleteProduct('${p.id}')" class="p-1.5 text-slate-500 hover:text-rose-600 rounded-lg hover:bg-slate-100" title="刪除商品">
                                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                                </button>
                            </td>
                        </tr>
                    `;
                }).join('');
            }
            lucide.createIcons();
        }

        // Inline Editing Functionality (Requirement 2)
        function makeInlineEditable(element, productId, fieldName, type = 'text') {
            const product = appState.products.find(p => p.id === productId);
            if (!product) return;

            const currentValue = product[fieldName];
            const input = document.createElement('input');
            input.type = type;
            input.value = currentValue;
            input.className = "w-28 px-2 py-1 text-sm border-2 border-blue-500 rounded-lg focus:outline-none font-mono bg-white shadow-sm";

            element.replaceWith(input);
            input.focus();
            if (type === 'text') input.select();

            function saveInlineEdit() {
                let newValue = input.value.trim();
                if (type === 'number') {
                    newValue = parseFloat(newValue) || 0;
                    if (newValue < 0) newValue = 0;
                }

                if (newValue !== "" && newValue !== currentValue) {
                    product[fieldName] = newValue;
                    saveData();
                    showToast(`已成功修改 ${fieldName === 'name' ? '名稱' : fieldName === 'price' ? '售價' : '成本'}`);
                }
                renderProducts();
                renderDashboard();
            }

            input.addEventListener('blur', saveInlineEdit);
            input.addEventListener('keydown', (e) => {
                if (e.key === 'Enter') {
                    input.blur();
                } else if (e.key === 'Escape') {
                    renderProducts();
                }
            });
        }

        // Product Modal Functions
        function openProductModal(editId = null) {
            const modal = document.getElementById('modal-product');
            const title = document.getElementById('modal-product-title');
            document.getElementById('form-product').reset();
            document.getElementById('prod-id').value = '';

            if (editId) {
                const prod = appState.products.find(p => p.id === editId);
                if (prod) {
                    title.innerText = "編輯商品資訊";
                    document.getElementById('prod-id').value = prod.id;
                    document.getElementById('prod-sku').value = prod.sku;
                    document.getElementById('prod-name').value = prod.name;
                    document.getElementById('prod-price').value = prod.price;
                    document.getElementById('prod-cost').value = prod.cost;
                    document.getElementById('prod-stock').value = prod.stock;
                }
            } else {
                title.innerText = "新增商品";
                document.getElementById('prod-sku').value = 'SKU-' + Math.floor(1000 + Math.random() * 9000);
            }

            calcModalMargin();
            modal.classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('modal-product').classList.add('hidden');
        }

        function calcModalMargin() {
            const price = parseFloat(document.getElementById('prod-price').value) || 0;
            const cost = parseFloat(document.getElementById('prod-cost').value) || 0;
            const profit = price - cost;
            const margin = price > 0 ? ((profit / price) * 100).toFixed(1) : "0.0";

            document.getElementById('modal-preview-profit').innerText = `$${profit.toLocaleString()}`;
            document.getElementById('modal-preview-margin').innerText = `${margin}%`;
        }

        function handleProductSubmit(e) {
            e.preventDefault();
            const id = document.getElementById('prod-id').value;
            const sku = document.getElementById('prod-sku').value.trim();
            const name = document.getElementById('prod-name').value.trim();
            const price = parseFloat(document.getElementById('prod-price').value) || 0;
            const cost = parseFloat(document.getElementById('prod-cost').value) || 0;
            const stock = parseInt(document.getElementById('prod-stock').value) || 0;

            if (id) {
                // Update
                const prod = appState.products.find(p => p.id === id);
                if (prod) {
                    prod.sku = sku;
                    prod.name = name;
                    prod.price = price;
                    prod.cost = cost;
                    prod.stock = stock;
                }
                showToast("商品資訊已成功更新");
            } else {
                // Create
                const newProd = {
                    id: 'p_' + Date.now(),
                    sku,
                    name,
                    price,
                    cost,
                    stock,
                    createdAt: new Date().toISOString()
                };
                appState.products.push(newProd);
                showToast("成功新增新商品");
            }

            saveData();
            closeProductModal();
            renderProducts();
            renderDashboard();
        }

        function editProduct(id) {
            openProductModal(id);
        }

        function confirmDeleteProduct(id) {
            const prod = appState.products.find(p => p.id === id);
            if (!prod) return;

            showConfirmModal(
                "刪除商品確認",
                `您確定要刪除商品「${prod.name}」嗎？這將不會影響歷史已產生的銷售紀錄。`,
                () => {
                    appState.products = appState.products.filter(p => p.id !== id);
                    saveData();
                    renderProducts();
                    renderDashboard();
                    showToast("商品已成功刪除");
                }
            );
        }

        // --- Sales Management ---
        function renderSales() {
            const tbody = document.getElementById('sales-tbody');
            const sortedSales = [...appState.sales].sort((a, b) => new Date(b.date) - new Date(a.date));

            if (sortedSales.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="8" class="text-center py-12 text-slate-400">
                            <i data-lucide="receipt" class="w-10 h-10 mx-auto mb-2 opacity-40"></i>
                            <p class="text-base font-medium text-slate-600">尚無銷售記帳記錄</p>
                            <p class="text-xs text-slate-400 mt-1">點擊上方「快速開單」建立銷售與庫存扣減紀錄</p>
                        </td>
                    </tr>
                `;
            } else {
                tbody.innerHTML = sortedSales.map(s => {
                    const profit = s.totalPrice - s.totalCost;
                    return `
                        <tr class="hover:bg-slate-50 transition-colors">
                            <td class="px-6 py-4 font-mono text-xs text-slate-500">${s.date}</td>
                            <td class="px-6 py-4 font-semibold text-slate-800">${escapeHtml(s.productName)}</td>
                            <td class="px-6 py-4 text-center font-bold">${s.qty}</td>
                            <td class="px-6 py-4 text-right font-mono">$${s.price.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono text-slate-500">$${s.cost.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono font-bold text-slate-900">$${s.totalPrice.toLocaleString()}</td>
                            <td class="px-6 py-4 text-right font-mono font-bold ${profit >= 0 ? 'text-emerald-600' : 'text-rose-600'}">
                                $${profit.toLocaleString()}
                            </td>
                            <td class="px-6 py-4 text-center">
                                <button onclick="confirmDeleteSales('${s.id}')" class="p-1.5 text-slate-400 hover:text-rose-600 rounded-lg hover:bg-slate-100" title="撤銷此銷貨單（還原庫存）">
                                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                                </button>
                            </td>
                        </tr>
                    `;
                }).join('');
            }
            lucide.createIcons();
        }

        function openSalesModal() {
            if (appState.products.length === 0) {
                showToast("請先新增至少一項商品後再進行銷售開單", "error");
                return;
            }

            const modal = document.getElementById('modal-sales');
            const select = document.getElementById('sales-product-select');
            
            // Populate select dropdown
            select.innerHTML = appState.products.map(p => 
                `<option value="${p.id}">${escapeHtml(p.name)} (庫存: ${p.stock}) - $${p.price}</option>`
            ).join('');

            // Set today's date
            document.getElementById('sales-date').value = new Date().toISOString().split('T')[0];
            document.getElementById('sales-qty').value = 1;

            onSelectSalesProduct();
            modal.classList.remove('hidden');
        }

        function closeSalesModal() {
            document.getElementById('modal-sales').classList.add('hidden');
        }

        function onSelectSalesProduct() {
            const select = document.getElementById('sales-product-select');
            const productId = select.value;
            const product = appState.products.find(p => p.id === productId);

            if (product) {
                document.getElementById('sales-price').value = product.price;
                document.getElementById('sales-cost').value = product.cost;
                calcSalesModalTotals();
            }
        }

        function calcSalesModalTotals() {
            const qty = parseInt(document.getElementById('sales-qty').value) || 0;
            const price = parseFloat(document.getElementById('sales-price').value) || 0;
            const cost = parseFloat(document.getElementById('sales-cost').value) || 0;

            const totalPrice = qty * price;
            const totalCost = qty * cost;
            const profit = totalPrice - totalCost;
            const margin = totalPrice > 0 ? ((profit / totalPrice) * 100).toFixed(1) : "0.0";

            document.getElementById('sales-modal-total-price').innerText = `$${totalPrice.toLocaleString()}`;
            document.getElementById('sales-modal-total-cost').innerText = `$${totalCost.toLocaleString()}`;
            document.getElementById('sales-modal-profit').innerText = `$${profit.toLocaleString()}`;
            document.getElementById('sales-modal-margin').innerText = `${margin}%`;
        }

        function handleSalesSubmit(e) {
            e.preventDefault();
            const productId = document.getElementById('sales-product-select').value;
            const qty = parseInt(document.getElementById('sales-qty').value) || 0;
            const price = parseFloat(document.getElementById('sales-price').value) || 0;
            const cost = parseFloat(document.getElementById('sales-cost').value) || 0;
            const date = document.getElementById('sales-date').value;

            const product = appState.products.find(p => p.id === productId);
            if (!product) return;

            // Deduct inventory stock
            product.stock = Math.max(0, product.stock - qty);

            const newSale = {
                id: 's_' + Date.now(),
                productId: product.id,
                productName: product.name,
                qty: qty,
                price: price,
                cost: cost,
                totalPrice: qty * price,
                totalCost: qty * cost,
                date: date,
                createdAt: new Date().toISOString()
            };

            appState.sales.push(newSale);
            saveData();
            closeSalesModal();
            renderProducts();
            renderSales();
            renderDashboard();
            showToast("銷售記帳成功，庫存已扣減");
        }

        function confirmDeleteSales(saleId) {
            const sale = appState.sales.find(s => s.id === saleId);
            if (!sale) return;

            showConfirmModal(
                "刪除銷售紀錄",
                `確定要刪除這筆銷售紀錄嗎？系統將補回該商品 ${sale.qty} 個庫存。`,
                () => {
                    // Restore stock if product exists
                    const prod = appState.products.find(p => p.id === sale.productId);
                    if (prod) {
                        prod.stock += sale.qty;
                    }
                    appState.sales = appState.sales.filter(s => s.id !== saleId);
                    saveData();
                    renderProducts();
                    renderSales();
                    renderDashboard();
                    showToast("銷售紀錄已撤銷，庫存已歸還");
                }
            );
        }

        // --- System Settings & Data Utilities ---
        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(appState, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `ERP_Backup_${new Date().toISOString().split('T')[0]}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
            showToast("資料庫備份檔已成功匯出");
        }

        function importData(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const imported = JSON.parse(e.target.result);
                    if (imported && Array.isArray(imported.products) && Array.isArray(imported.sales)) {
                        appState = imported;
                        saveData();
                        initYearSelector();
                        renderDashboard();
                        renderProducts();
                        renderSales();
                        showToast("備份資料已成功匯入");
                    } else {
                        showToast("無效的備份檔案格式", "error");
                    }
                } catch (err) {
                    showToast("解析備份檔案失敗", "error");
                }
            };
            reader.readAsText(file);
        }

        function confirmResetAll() {
            showConfirmModal(
                "高風險：重置整個 ERP 系統",
                "確定要清空所有商品庫存與銷售紀錄嗎？此動作無法撤銷！",
                () => {
                    appState = { products: [], sales: [] };
                    saveData();
                    initYearSelector();
                    renderDashboard();
                    renderProducts();
                    renderSales();
                    showToast("系統已成功重置為初始狀態");
                }
            );
        }

        // --- Custom UI Dialogs & Toast Helpers ---
        function showToast(msg, type = 'success') {
            const toast = document.getElementById('toast');
            const toastMsg = document.getElementById('toast-msg');
            const toastIcon = document.getElementById('toast-icon');

            toastMsg.innerText = msg;
            if (type === 'error') {
                toastIcon.className = "w-5 h-5 text-rose-400";
            } else {
                toastIcon.className = "w-5 h-5 text-emerald-400";
            }

            toast.classList.remove('opacity-0', 'translate-y-4', 'pointer-events-none');
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-4', 'pointer-events-none');
            }, 3000);
        }

        function showConfirmModal(title, desc, onConfirm) {
            document.getElementById('confirm-title').innerText = title;
            document.getElementById('confirm-desc').innerText = desc;
            confirmActionHandler = onConfirm;

            document.getElementById('btn-confirm-action').onclick = () => {
                if (confirmActionHandler) confirmActionHandler();
                closeConfirmModal();
            };

            document.getElementById('modal-confirm').classList.remove('hidden');
        }

        function closeConfirmModal() {
            document.getElementById('modal-confirm').classList.add('hidden');
        }

        function escapeHtml(str) {
            if (!str) return '';
            return str.replace(/&/g, "&amp;")
                      .replace(/</g, "&lt;")
                      .replace(/>/g, "&gt;")
                      .replace(/"/g, "&quot;")
                      .replace(/'/g, "&#039;");
        }

        // Initialization
        window.onload = function() {
            loadData();
            initYearSelector();
            renderDashboard();
            updateBadges();
            lucide.createIcons();
        };
    </script>
</body>
</html>
