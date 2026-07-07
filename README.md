<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>追蹤管理系統</title>
    <!-- Tailwind CSS - 清新明亮色彩系統 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght=300;400;500;600;700;900&display=swap');
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Microsoft JhengHei", sans-serif;
        }
        @keyframes subtle-float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-4px); }
        }
        .float-subtle {
            animation: subtle-float 3s ease-in-out infinite;
        }
        /* 自訂美化捲軸 */
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: transparent;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 10px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        /* 固定兩列文字截斷，防止表格高度擠壓 */
        .line-clamp-2 {
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;  
            overflow: hidden;
        }
    </style>
</head>
<body class="bg-slate-50/50 text-slate-700 min-h-screen flex flex-col antialiased">

    <!-- 頂部導航列 -->
    <header class="bg-white border-b border-slate-200/80 sticky top-0 z-40 px-6 py-4 shadow-sm">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
            
            <div class="flex items-center gap-3">
                <div class="p-2.5 bg-emerald-50 text-emerald-600 rounded-2xl border border-emerald-100 shadow-sm">
                    <!-- SVG 扳手 & 齒輪 -->
                    <svg class="w-6 h-6 animate-pulse" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                        <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" />
                    </svg>
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-tight text-slate-800 flex items-center gap-2">
                        追蹤管理系統
                    </h1>
                    <p class="text-xs text-slate-400 mt-0.5 font-medium">雙軌即時監控：日期合規到期與現場機具保養時數</p>
                </div>
            </div>

            <div class="flex items-center flex-wrap gap-3">
                <!-- 時數快速登錄按鈕 -->
                <button
                    onclick="openHoursLogModal()"
                    class="flex items-center gap-2 bg-gradient-to-r from-amber-500 to-orange-500 hover:from-amber-600 hover:to-orange-600 text-white font-extrabold px-4 py-2.5 rounded-2xl transition shadow-sm active:scale-95 text-xs"
                >
                    <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                        <circle cx="12" cy="12" r="10" />
                        <polyline points="12 6 12 12 16 14" />
                    </svg>
                    <span>🛠 時數登錄</span>
                </button>
                <!-- 權限管理機制 -->
                <button
                    id="adminPermissionBtn"
                    onclick="toggleAdminPermission()"
                    class="flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold border transition shadow-sm"
                >
                    <!-- JS 動態更換鎖頭狀態與樣式 -->
                </button>

                <!-- 同仁下拉選取機制 -->
                <div class="flex items-center bg-slate-100/75 border border-slate-200 rounded-2xl p-1 gap-1">
                    <span class="text-xs text-slate-400 pl-2.5 pr-1 flex items-center gap-1 font-semibold">
                        <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" />
                            <circle cx="12" cy="7" r="4" />
                        </svg> 操作人:
                    </span>
                    <select
                        id="currentUserSelect"
                        onchange="onUserSelectChange(this)"
                        class="bg-transparent text-xs text-slate-800 font-bold py-1 px-2.5 rounded-xl border-0 focus:ring-0 cursor-pointer focus:outline-none"
                    >
                        <!-- 動態渲染 -->
                    </select>
                    <button
                        onclick="openEmployeeModalDirect()"
                        class="p-1 hover:bg-slate-200 text-slate-400 hover:text-slate-700 rounded-lg transition"
                        title="管理同仁名冊"
                    >
                        <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <circle cx="12" cy="12" r="3" />
                            <path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 1 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 1 1-2.83 2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 1 1 2.83-2.83l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 1 1 2.83 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z" />
                        </svg>
                    </button>
                </div>
            </div>

        </div>
    </header>
    <!-- 主版面 -->
    <div class="flex-1 max-w-7xl w-full mx-auto p-6 grid grid-cols-1 lg:grid-cols-12 gap-6">
        
        <!-- ==================== 左側：全天候即時預警區 ==================== -->
        <section class="lg:col-span-4 flex flex-col gap-6">
            
            <!-- 1. 待辦事項提醒區 -->
            <div class="bg-white rounded-2xl border border-slate-200/60 p-5 shadow-sm flex flex-col max-h-[380px]">
                <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-3.5">
                    <h2 class="font-extrabold text-sm text-slate-800 flex items-center gap-2">
                        <svg class="w-4 h-4 text-amber-500 float-subtle" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9" />
                            <path d="M13.73 21a2 2 0 0 1-3.46 0" />
                        </svg>
                        待辦事項提醒 <span id="dateAlertCount" class="text-amber-600 bg-amber-50 px-2 py-0.5 rounded-full text-xs font-mono ml-1">0</span>
                    </h2>
                </div>

                <div id="dateAlertContainer" class="flex-1 overflow-y-auto space-y-2.5 pr-1 custom-scrollbar">
                    <!-- 動態渲染 -->
                </div>
            </div>

            <!-- 2. 機具保養提醒區 -->
            <div class="bg-white rounded-2xl border border-slate-200/60 p-5 shadow-sm flex flex-col max-h-[380px]">
                <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-3.5">
                    <h2 class="font-extrabold text-sm text-slate-800 flex items-center gap-2">
                        <svg class="w-4 h-4 text-rose-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" />
                        </svg>
                        機具保養提醒 <span id="machineAlertCount" class="text-rose-600 bg-rose-50 px-2 py-0.5 rounded-full text-xs font-mono ml-1">0</span>
                    </h2>
                </div>

                <div id="machineAlertContainer" class="flex-1 overflow-y-auto space-y-2.5 pr-1 custom-scrollbar">
                    <!-- 動態渲染 -->
                </div>
            </div>

        </section>

        <!-- ==================== 右側：核心管理工作台 ==================== -->
        <main class="lg:col-span-8 flex flex-col gap-6">
            
            <!-- 分頁頁籤 -->
            <div class="flex items-center justify-between bg-white p-2 rounded-2xl border border-slate-200/60 shadow-sm">
                <div class="flex flex-wrap gap-1">
                    <button
                        id="tab-date-reminders"
                        onclick="switchTab('date-reminders')"
                        class="flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 bg-emerald-600 text-white shadow-sm"
                    >
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <rect x="3" y="4" width="18" height="18" rx="2" ry="2" />
                            <line x1="16" y1="2" x2="16" y2="6" />
                            <line x1="8" y1="2" x2="8" y2="6" />
                            <line x1="3" y1="10" x2="21" y2="10" />
                        </svg>
                        <span>📅 待辦事項設定</span>
                    </button>

                    <button
                        id="tab-machinery-hours"
                        onclick="switchTab('machinery-hours')"
                        class="flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50"
                    >
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" />
                        </svg>
                        <span>🚜 機具保養清冊</span>
                    </button>

                    <button
                        id="tab-settings"
                        onclick="switchTab('settings')"
                        class="flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50"
                    >
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <line x1="4" y1="21" x2="4" y2="14" />
                            <line x1="4" y1="10" x2="4" y2="3" />
                            <line x1="12" y1="21" x2="12" y2="12" />
                            <line x1="12" y1="8" x2="12" y2="3" />
                            <line x1="20" y1="21" x2="20" y2="14" />
                            <line x1="20" y1="12" x2="20" y2="3" />
                            <line x1="1" y1="14" x2="7" y2="14" />
                            <line x1="9" y1="8" x2="15" y2="8" />
                            <line x1="17" y1="16" x2="23" y2="16" />
                        </svg>
                        <span>⚙️ 基礎清冊管理</span>
                    </button>
                </div>
            </div>

            <!-- TAB 1: 待辦事項設定 -->
            <div id="content-date-reminders" class="space-y-6">
                
                <!-- 新增提醒品項表單 (開放給所有人) -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm relative">
                    <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2">
                        <svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19" /><line x1="5" y1="12" x2="19" y2="12" /></svg>
                        建立新到期通知品項
                    </h3>
                    
                    <form onsubmit="addDateReminder(event)" class="grid grid-cols-1 md:grid-cols-12 gap-4">
                        <div class="md:col-span-8">
                            <label class="block text-[11px] font-bold text-slate-500 
mb-1.5">1. 品項名稱 *</label>
                            <input
                                id="r-itemName"
                                type="text"
                                placeholder="例如：消防合格證更換、防汛砂包定期檢查..."
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none focus:ring-1 focus:ring-emerald-500 focus:bg-white transition font-medium"
                                required
                            />
                        </div>

                        <div class="md:col-span-4">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">2. 部門歸屬</label>
                            <select
                                id="r-department"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none font-medium"
                            >
                                <!-- 動態載入 -->
                            </select>
                        </div>

                        <div class="md:col-span-4">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">3. 擔當同仁</label>
                            <select
                                id="r-manager"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none font-medium"
                            >
                                <!-- 動態載入 -->
                            </select>
                        </div>

                        <div class="md:col-span-4">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">4. 到期日期 *</label>
                            <input
                                id="r-expiryDate"
                                type="date"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none font-medium"
                                required
                            />
                        </div>

                        <div class="md:col-span-4">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">5. 提醒警示天數 (三個設定)</label>
                            <div class="grid grid-cols-3 gap-1.5">
                                <input id="r-remindDays1" type="number" value="30" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                                <input id="r-remindDays2" type="number" value="14" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                                <input id="r-remindDays3" type="number" value="7" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                            </div>
                        </div>

                        <div class="md:col-span-9">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">6. 備註說明</label>
                            <input
                                id="r-remark"
                                type="text"
                                placeholder="選填，可在此紀錄文件編號、置放處或備辦手續細節..."
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium"
                            />
                        </div>
                        <div class="md:col-span-3 flex items-end">
                            <button
                                type="submit"
                                class="w-full bg-slate-800 hover:bg-slate-950 text-white font-extrabold py-2 px-4 rounded-xl text-xs shadow-sm transition"
                            >
                                新增此待辦品項
                            </button>
                        </div>
                    </form>
                </div>

                <!-- 到期提醒表格清冊 -->
                <div class="bg-white rounded-2xl border border-slate-200/60 shadow-sm overflow-hidden">
                    
                    <div class="p-4 bg-slate-50/70 border-b border-slate-200/60 flex flex-col sm:flex-row justify-between items-center gap-3">
                        <!-- 搜尋 -->
                        <div class="relative w-full sm:w-72">
                            <svg class="absolute left-3 top-2.5 w-3.5 h-3.5 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                                <circle cx="11" cy="11" r="8" /><line x1="21" y1="21" x2="16.65" y2="16.65" />
                            </svg>
                            <input 
                                id="searchQuery"
                                type="text"
                                oninput="renderDateTable()"
                                placeholder="搜尋品項、部門、負責人..."
                                class="w-full pl-8 pr-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs focus:outline-none font-medium"
                            />
                        </div>

                        <!-- 狀態切換模式 -->
                        <div class="flex bg-slate-200/50 border border-slate-250 p-0.5 rounded-xl text-xs shrink-0 font-bold">
                            <button onclick="setDateFilter('all')" id="filter-all" class="px-3 py-1.5 rounded-lg transition-all">全部</button>
                            <button onclick="setDateFilter('pending')" id="filter-pending" class="px-3 py-1.5 rounded-lg transition-all bg-white text-slate-800 shadow-sm font-extrabold">未完成</button>
                            <button onclick="setDateFilter('completed')" id="filter-completed" class="px-3 py-1.5 rounded-lg transition-all">已完成</button>
                        </div>
                    </div>

                    <!-- 表格 -->
                    <div class="overflow-x-auto custom-scrollbar">
                        <table class="w-full text-left text-xs table-fixed min-w-[850px]">
                            <thead>
                                <tr class="bg-slate-50 border-b border-slate-200 text-slate-500 font-bold uppercase">
                                    <th class="px-4 py-3 w-16 text-center">狀態</th>
                                    <th class="px-4 py-3 w-28 text-center">部門</th>
                                    <th class="px-4 py-3">品項 / 備註</th>
                                    <th class="px-4 py-3 w-28">擔當同仁</th>
                                    <th class="px-4 py-3 w-36">到期日期</th>
                                    <th class="px-4 py-3 w-40">警示狀態</th>
                                    <th class="px-4 py-3 w-14 text-center">刪除</th>
                                </tr>
                            </thead>
                            <tbody id="dateReminderTableBody" class="divide-y divide-slate-100 font-semibold text-slate-600">
                                <!-- 動態渲染 -->
                            </tbody>
                        </table>
                    </div>

                </div>

            </div>

            <!-- TAB 2: 機具保養清冊 -->
            <div id="content-machinery-hours" class="space-y-6 hidden">
                
                <!-- 新增時數機具表單 (開放給所有人) -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm relative">
                    <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2">
                        <svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19" /><line x1="5" y1="12" x2="19" y2="12" /></svg>
                        新增時數追蹤機具
                    </h3>
                    
                    <form onsubmit="addMachine(event)" class="grid grid-cols-1 md:grid-cols-12 gap-4">
                        <div class="md:col-span-8">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">機具名稱 *</label>
                            <input
                                id="m-machineName"
                                type="text"
                                placeholder="如: 大型挖土機, 250KV發電機"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium"
                                required
                            />
                        </div>

                        <div class="md:col-span-4">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">歸屬部門</label>
                            <select
                                id="m-department"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium"
                            >
                                <!-- 動態載入 -->
                            </select>
                        </div>

                        <div class="md:col-span-3">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">初始累計時數 (h)</label>
                            <input
                                id="m-currentHours"
                                type="number"
                                min="0"
                                value="0"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium"
                            />
                        </div>

                        <div class="md:col-span-3">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">下次保養目標時數 (h)</label>
                            <input
                                id="m-nextMaintenanceHours"
                                type="number"
                                min="1"
                                value="250"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium"
                            />
                        </div>

                        <div class="md:col-span-3">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">警示臨界時數 (h) ⚠️</label>
                            <input
                                id="m-warningThreshold"
                                type="number"
                                min="1"
                                value="50"
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium"
                            />
                        </div>

                        <div class="md:col-span-3 flex items-end">
                            <button
                                type="submit"
                                class="w-full bg-slate-800 hover:bg-slate-950 text-white font-extrabold py-2 px-4 rounded-xl text-xs transition font-medium"
                            >
                                建立機具檔案
                            </button>
                        </div>

                        <div class="md:col-span-12">
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">保養規格備註說明</label>
                            <input
                                id="m-remark"
                                type="text"
                                placeholder="選填，可註記機油耗材尺寸、規格..."
                                class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium"
                            />
                        </div>
                    </form>
                </div>

                <!-- 機具保養清冊表格 -->
                <div class="bg-white rounded-2xl border border-slate-200/60 shadow-sm overflow-hidden">
                    <div class="p-4 bg-slate-50/70 border-b border-slate-200/60 flex flex-col sm:flex-row justify-between items-center gap-3">
                        <div class="flex items-center gap-2">
                            <svg class="w-4 h-4 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                                <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" />
                            </svg>
                            <h3 class="font-extrabold text-slate-800 text-xs">機具保養清冊</h3>
                        </div>
                    </div>

                    <!-- 解決文字折行問題的排版結構 -->
                    <div class="overflow-x-auto custom-scrollbar">
                        <table class="w-full text-left text-xs table-fixed min-w-[850px]">
                            <thead>
                                <tr class="bg-slate-50 border-b border-slate-200 text-slate-500 font-bold uppercase">
                                    <th class="px-4 py-3 w-24 text-center">部門</th>
                                    <th class="px-4 py-3">機具名稱</th>
                                    <th class="px-4 py-3 w-48">保養時數 / 備註</th>
                                    <th class="px-4 py-3 w-32">警示狀態</th>
                                    <th class="px-4 py-3 w-36 text-center">現場時數登錄</th>
                                    <th class="px-4 py-3 w-14 text-center">刪除</th>
                                </tr>
                            </thead>
                            <tbody id="machineTableBody" class="divide-y divide-slate-100 font-semibold text-slate-600">
                                <!-- 動態渲染 -->
                            </tbody>
                        </table>
                    </div>

                </div>

            </div>

            <!-- TAB 3: 基礎清冊管理 (管理權限保護) -->
            <div id="content-settings" class="space-y-6 hidden">
                
                <div class="relative">
                    <div id="settingsLockOverlay" class="absolute inset-0 bg-white/80 backdrop-blur-[1px] rounded-2xl flex flex-col items-center justify-center z-10 transition-all">
                        <div class="bg-slate-800 text-white text-xs px-4 py-2.5 rounded-xl font-bold flex items-center gap-2 shadow-md cursor-pointer hover:bg-slate-900 animate-pulse" onclick="triggerAdminModal()">
                            <svg class="w-4 h-4 text-amber-400" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg>
                            請先解鎖頂部「管理權限」以編輯基礎清冊資料
                        </div>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        
                        <!-- 部門管理 -->
                        <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                            <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2">
                                <svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                                    <polygon points="12 2 2 7 12 12 22 7 12 2" /><polyline points="2 17 12 22 22 17" /><polyline points="2 12 12 17 22 12" />
                                </svg>
                                管理部門選單分類
                            </h3>

                            <div class="flex gap-2 mb-4">
                              <input
                                id="newDeptName"
                                type="text"
                                placeholder="新增部門分類"
                                class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none"
                              />
                              <button
                                onclick="addDepartment()"
                                class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 rounded-xl text-xs"
                              >
                                新增
                              </button>
                            </div>

                            <div id="settingsDeptList" class="space-y-1.5 max-h-56 overflow-y-auto pr-1">
                                <!-- 動態渲染 -->
                            </div>
                        </div>

                        <!-- 同仁管理 -->
                        <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                            <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2">
                                <svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                                    <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2" /><circle cx="9" cy="7" r="4" /><path d="M23 21v-2a4 4 0 0 0-3-3.87" /><path d="M16 3.13a4 4 0 0 1 0 7.75" />
                                </svg>
                                管理操作同仁名冊
                            </h3>

                            <div class="flex gap-2 mb-4">
                              <input
                                id="newEmployeeName"
                                type="text"
                                placeholder="新增同仁姓名"
                                class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none"
                              />
                              <button
                                onclick="addEmployee()"
                                class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 rounded-xl text-xs"
                              >
                                新增
                              </button>
                            </div>

                            <div id="settingsEmpList" class="space-y-1.5 max-h-56 overflow-y-auto pr-1">
                                <!-- 動態渲染 -->
                            </div>
                        </div>

                    </div>
                </div>
            </div>

        </main>

    </div>

    <!-- ==========================================
        MODALS (自訂對話彈窗)
        ========================================== -->

    <!-- 追蹤項目詳細內容彈窗 -->
    <div id="itemDetailModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl animate-scaleIn">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4">
                <h3 id="detailModalTitle" class="font-extrabold text-slate-800 text-sm flex items-center gap-1.5">
                    <!-- JS 動態更換標題 -->
                </h3>
                <button 
                    onclick="closeDetailModal()"
                    class="text-slate-400 hover:text-slate-700 font-bold text-sm"
                >
                    ✕
                </button>
            </div>
            
            <!-- 詳情展示內容 -->
            <div id="detailModalContent" class="space-y-3.5 text-xs text-slate-600 custom-scrollbar max-h-96 overflow-y-auto pr-1">
                <!-- JS 動態渲染 -->
            </div>

            <div class="mt-6 pt-3 border-t border-slate-100 flex justify-end">
                <button
                    onclick="closeDetailModal()"
                    class="bg-slate-800 hover:bg-slate-950 text-white font-extrabold px-5 py-2 rounded-xl text-xs transition shadow-sm active:scale-95"
                >
                    關閉視窗
                </button>
            </div>
        </div>
    </div>

    <!-- 加密驗證彈窗 -->
    <div id="adminModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-sm shadow-2xl">
            <div class="flex items-center gap-2 text-indigo-600 mb-3 border-b border-slate-100 pb-3">
                <svg class="w-5 h-5 text-indigo-500" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg>
                <h3 class="font-extrabold text-base text-slate-800">解鎖管理權限</h3>
            </div>
            <div class="space-y-4">
                <p class="text-xs text-slate-400 leading-relaxed font-medium">
                    請輸入系統管理密碼，以進行「刪除與基礎清冊異動」等管制操作。
                </p>
                <div>
                    <input
                        id="adminPasswordInput"
                        type="password"
                        placeholder="請輸入解鎖密碼"
                        class="w-full bg-slate-50 border border-slate-250 rounded-2xl px-4 py-3 text-center text-sm font-bold tracking-widest text-slate-800 focus:ring-2 focus:ring-indigo-400 focus:bg-white focus:outline-none transition"
                        onkeydown="onAdminPasswordKeydown(event)"
                    />
                </div>
                
                <!-- 快捷解鎖小幫手 -->
                <div class="text-center">
                    <button type="button" onclick="quickBypassAdmin()" class="text-[11px] text-blue-500 hover:text-blue-700 underline font-extrabold">
                
                    </button>
                </div>

                <div class="flex gap-2">
                    <button
                        type="button"
                        onclick="closeAdminModal()"
                        class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition"
                    >
                        取消
                    </button>
                    <button
                        type="button"
                        onclick="verifyAdminPassword()"
                        class="flex-1 py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold rounded-xl text-xs transition shadow-sm"
                    >
                        送出驗證
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 1. 現場同仁快速登錄時數彈窗 -->
    <div id="hoursLogModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl">
            <div class="flex items-center gap-2 text-amber-500 mb-3 border-b border-slate-100 pb-3">
                <svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="12" cy="12" r="10" /><polyline points="12 6 12 12 16 14" /></svg>
                <h3 class="font-extrabold text-base text-slate-800">現場同仁/師傅快速登錄目前時數</h3>
            </div>

            <form onsubmit="submitHoursLog(event)" class="space-y-4">
                
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1.5">1. 請選擇欲登錄時數的機具 *</label>
                    <select
                        id="logMachineSelect"
                        class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2.5 text-xs text-slate-800 font-bold focus:outline-none"
                    >
                        <!-- 動態 -->
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1.5">
                        2. 請輸入最新的累計總時數 (h) *
                    </label>
                    <input
                        id="logHoursInput"
                        type="number"
                        min="0"
                        placeholder="請輸入目前累計的整數時數"
                        class="w-full bg-slate-50 border border-slate-250 rounded-2xl px-4 py-3 text-xl text-center font-bold font-mono text-amber-600 focus:ring-2 focus:ring-amber-400 focus:bg-white focus:outline-none transition"
                        required
                    />
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1.5">3. 記錄回報人 *</label>
                    <select
                        id="logOperatorSelect"
                        class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-bold focus:outline-none"
                    >
                        <!-- 動態 -->
                    </select>
                </div>

                <div class="flex gap-2 pt-2">
                    <button
                        type="button"
                        onclick="closeHoursLogModal()"
                        class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition"
                    >
                        取消
                    </button>
                    <button
                        type="submit"
                        class="flex-1 py-2.5 bg-amber-500 hover:bg-amber-600 text-white font-extrabold rounded-xl text-xs transition shadow-sm"
                    >
                        確定登錄時數
                    </button>
                </div>

            </form>
        </div>
    </div>

    <!-- 2. 保養結案設定下次目標彈窗 -->
    <div id="maintCompleteModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl">
            <div class="flex items-center gap-2 text-emerald-600 mb-3 border-b border-slate-100 pb-3">
                <svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12" /></svg>
                <h3 class="font-extrabold text-base text-slate-800">機具定期保養結案</h3>
            </div>

            <form onsubmit="submitMaintComplete(event)" class="space-y-4">
                <input type="hidden" id="maintMachineId" />
                <p class="text-xs text-slate-400 leading-relaxed font-medium">
                    確定已為此機具完成所有定期維護與更換。
                    請指定這台機具的 <span class="text-emerald-600 font-black">「下次保養目標時數」</span>，以重新開啟定時數保養警報。
                </p>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1.5">下次需進行保養的總累計時數 (h) *</label>
                    <input
                        id="maintNextHoursInput"
                        type="number"
                        min="1"
                        class="w-full bg-slate-50 border border-slate-250 rounded-2xl px-4 py-3 text-xl text-center font-bold font-mono text-emerald-600 focus:ring-2 focus:ring-emerald-400 focus:bg-white focus:outline-none transition"
                        required
                    />
                    <p class="text-[10px] text-slate-400 mt-1.5 font-medium">
                        💡 建議：若每 250 小時保養一次，此次目標一般為「當前時數 + 250」。
                    </p>
                </div>

                <div class="flex gap-2 pt-2">
                    <button
                        type="button"
                        onclick="closeMaintCompleteModal()"
                        class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition"
                    >
                        取消
                    </button>
                    <button
                        type="submit"
                        class="flex-1 py-2.5 bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold rounded-xl text-xs transition shadow-sm"
                    >
                        確認結案保養
                    </button>
                </div>

            </form>
        </div>
    </div>

    <!-- 3. 同仁管理快捷視窗 -->
    <div id="employeeModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-sm shadow-2xl">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4">
                <h3 class="font-extrabold text-slate-800 text-sm flex items-center gap-1.5">
                    <svg class="w-4 h-4 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2" /><circle cx="9" cy="7" r="4" /></svg>
                    整理操作同仁名冊
                </h3>
                <button 
                    onclick="closeEmployeeModal()"
                    class="text-slate-400 hover:text-slate-700 font-bold text-xs focus:outline-none"
                >
                    關閉
                </button>
            </div>

            <div id="employeeModalLockedMsg" class="p-3 bg-rose-50 text-rose-700 rounded-xl text-xs font-bold text-center border border-rose-100 mb-3 hidden">
                ⚠️ 請先解鎖頂部「管理權限」以編輯或刪除操作同仁
            </div>

            <div class="space-y-4">
                <div class="flex gap-2" id="employeeModalAddForm">
                    <input
                        id="modalEmployeeName"
                        type="text"
                        placeholder="輸入新同仁姓名"
                        class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none"
                    />
                    <button
                        onclick="addEmployeeFromModal()"
                        class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold px-3 rounded-xl text-xs"
                    >
                        新增
                    </button>
                </div>

                <div id="modalEmpList" class="space-y-1.5 max-h-48 overflow-y-auto pr-1 custom-scrollbar">
                    <!-- 動態 -->
                </div>
            </div>
        </div>
    </div>

    <!-- 4. 自訂確認提示對話窗 -->
    <div id="confirmDialog" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white rounded-3xl max-w-sm w-full p-6 text-center flex flex-col items-center gap-4 shadow-2xl border border-slate-200/60">
            <div class="p-3 bg-rose-50 text-rose-500 rounded-full border border-rose-100">
                <svg class="w-6 h-6 animate-bounce" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z" /><line x1="12" y1="9" x2="12" y2="13" /><line x1="12" y1="17" x2="12.01" y2="17" /></svg>
            </div>
            <div>
                <h4 class="font-extrabold text-sm text-slate-800">操作確認通知</h4>
                <p id="confirmMessage" class="text-xs text-slate-500 mt-2 font-medium leading-relaxed">確認執行此操作嗎？</p>
            </div>
            <div class="flex gap-2 w-full pt-1">
                <button 
                    onclick="closeConfirmDialog(false)" 
                    class="flex-1 py-2 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition"
                >
                    取消
                </button>
                <button 
                    onclick="closeConfirmDialog(true)" 
                    class="flex-1 py-2 bg-rose-500 hover:bg-rose-600 text-white font-bold rounded-xl text-xs transition shadow-sm"
                >
                    確認
                </button>
            </div>
        </div>
    </div>

    <!-- 5. Toast 氣泡通知 -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 hidden">
        <div class="px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 border bg-slate-800 border-slate-700 text-white font-bold text-xs">
            <svg id="toastIcon" class="w-4 h-4 text-emerald-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12" /></svg>
            <span id="toastMessage">操作完成</span>
        </div>
    </div>

    <!-- 頁尾 -->
    <footer class="py-4 bg-white border-t border-slate-150 text-center text-[10px] text-slate-400 font-bold uppercase tracking-wider mt-auto">
        追蹤管理系統 © 2026 • 雲端與現場雙軌即時控制中樞
    </footer>

    <script>
        // ===================================================
        // 系統狀態設定 (資料庫與本機模擬引擎)
        // ===================================================
        let state = {
            activeTab: 'date-reminders',
            currentUser: '',
            dateFilter: 'pending', // 'all', 'pending', 'completed'
            isAdmin: false, // 管理權限解鎖狀態
            departments: ['總務部', '行政部', '儲運部', '工務部', '資訊部'],
            employees: ['張經理', '林課長', '陳專員', '王助理', '李工程師'],
            dateReminders: [
                {
                    id: 'init-1',
                    itemName: '高壓變電器定期檢驗換證',
                    department: '工務部',
                    manager: '林課長',
                    expiryDate: '2026-08-15',
                    remindDays1: 30,
                    remindDays2: 14,
                    remindDays3: 7,
                    remark: '需委託外部合格電機技師現場試驗與出具報告。',
                    completed: false,
                    completedBy: '',
                    completedAt: ''
                },
                {
                    id: 'init-2',
                    itemName: '防汛砂包與行車路口合格證到期更新',
                    department: '儲運部',
                    manager: '陳專員',
                    expiryDate: '2026-06-25',
                    remindDays1: 30,
                    remindDays2: 14,
                    remindDays3: 7,
                    remark: '檢查防水布與砂包填充，並向公所報備。',
                    completed: false,
                    completedBy: '',
                    completedAt: ''
                }
            ],
            machines: [
                {
                    id: 'init-m1',
                    machineName: '大型履帶式挖土機',
                    department: '工務部',
                    currentHours: 180,
                    nextMaintenanceHours: 250,
                    warningThreshold: 50,
                    remark: '指定使用15W-40耐磨液壓機油。',
                    lastUpdatedBy: '李工程師',
                    lastUpdatedAt: '2026-07-01 10:00:00'
                },
                {
                    id: 'init-m2',
                    machineName: '緊急備用柴油發電機',
                    department: '總務部',
                    currentHours: 245,
                    nextMaintenanceHours: 250,
                    warningThreshold: 30,
                    remark: '電瓶水及皮帶鬆緊度需於現場回報。',
                    lastUpdatedBy: '林課長',
                    lastUpdatedAt: '2026-07-02 14:30:00'
                }
            ]
        };

        // 載入 localStorage
        function loadState() {
            const saved = localStorage.getItem('tracking_system_state_v6');
            if (saved) {
                try {
                    state = JSON.parse(saved);
                } catch(e) {
                    console.error("Failed to parse saved state", e);
                }
            }
            if (!state.currentUser && state.employees.length > 0) {
                state.currentUser = state.employees[0];
            }
        }

        // 儲存狀態
        function saveState() {
            localStorage.setItem('tracking_system_state_v6', JSON.stringify(state));
            renderAll();
        }

        // ===================================================
        // 通用輔助工具與自訂 Popup
        // ===================================================
        function showToast(message, isSuccess = true) {
            const toastEl = document.getElementById('toast');
            const msgEl = document.getElementById('toastMessage');
            const iconEl = document.getElementById('toastIcon');
            msgEl.textContent = message;
            
            if (isSuccess) {
                toastEl.className = "fixed bottom-6 right-6 z-50 px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 border bg-slate-850 border-emerald-500 text-white font-bold text-xs bg-slate-900";
                iconEl.innerHTML = `<polyline points="20 6 9 17 4 12" />`;
                iconEl.className = "w-4 h-4 text-emerald-400";
            } else {
                toastEl.className = "fixed bottom-6 right-6 z-50 px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 border bg-slate-850 border-rose-500 text-white font-bold text-xs bg-slate-900";
                iconEl.innerHTML = `<circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>`;
                iconEl.className = "w-4 h-4 text-rose-400";
            }
            
            toastEl.classList.remove('hidden');
            setTimeout(() => {
                toastEl.classList.add('hidden');
            }, 3000);
        }

        let confirmCallback = null;
        function triggerConfirm(message, callback) {
            confirmCallback = callback;
            document.getElementById('confirmMessage').textContent = message;
            document.getElementById('confirmDialog').classList.remove('hidden');
        }

        function closeConfirmDialog(approved) {
            document.getElementById('confirmDialog').classList.add('hidden');
            if (approved && confirmCallback) {
                confirmCallback();
            }
            confirmCallback = null;
        }

        function getTodayDateString() {
            const today = new Date();
            return today.toISOString().split('T')[0];
        }

        // 民國日期格式化工具
        function formatROCDateString(dateStr) {
            if(!dateStr) return '';
            const parts = dateStr.split('-');
            if (parts.length === 3) {
                const rocYear = parseInt(parts[0], 10) - 1911;
                return `民國 ${rocYear} 年 ${parts[1]} 月 ${parts[2]} 日`;
            }
            return dateStr;
        }

        // ===================================================
        // 追蹤項目詳細內容彈窗展示邏輯
        // ===================================================
        function openDateItemDetail(id) {
            const item = state.dateReminders.find(r => r.id === id);
            if (!item) return;

            const modalTitle = document.getElementById('detailModalTitle');
            const modalContent = document.getElementById('detailModalContent');

            modalTitle.innerHTML = `
                <svg class="w-5 h-5 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <rect x="3" y="4" width="18" height="18" rx="2" ry="2" />
                    <line x1="16" y1="2" x2="16" y2="6" />
                    <line x1="8" y1="2" x2="8" y2="6" />
                    <line x1="3" y1="10" x2="21" y2="10" />
                </svg>
                <span>📅 待辦提醒詳細資料</span>
            `;

            modalContent.innerHTML = `
                <div class="grid grid-cols-3 gap-2 border-b border-slate-100 pb-3">
                    <div class="text-slate-400 font-bold">品項名稱：</div>
                    <div class="col-span-2 text-slate-800 font-extrabold text-sm">${item.itemName}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">歸屬部門：</div>
                    <div class="col-span-2 text-slate-800 font-bold">${item.department}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">擔當同仁：</div>
                    <div class="col-span-2 text-slate-800 font-bold">${item.manager}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">西元日期：</div>
                    <div class="col-span-2 font-mono text-slate-800 font-bold">${item.expiryDate}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">民國到期：</div>
                    <div class="col-span-2 text-emerald-700 font-extrabold">${formatROCDateString(item.expiryDate)}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">預警天數：</div>
                    <div class="col-span-2 text-slate-800 font-medium">到期前 ${item.remindDays1} 天、${item.remindDays2} 天、${item.remindDays3} 天發出警告</div>
                </div>
                <div class="border-t border-slate-100 pt-3">
                    <div class="text-slate-400 font-bold mb-1">備註說明：</div>
                    <div class="bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-slate-600 font-medium leading-relaxed">${item.remark || '（無額外備註資料）'}</div>
                </div>
                <div class="border-t border-slate-100 pt-3">
                    <div class="text-slate-400 font-bold mb-1">結案狀態：</div>
                    <div class="font-bold">
                        ${item.completed 
                            ? `<span class="text-emerald-600">✓ 已結案 (由同仁 [${item.completedBy}] 結案於 ${item.completedAt})</span>` 
                            : '<span class="text-amber-600">⚠️ 尚未結案 (未勾選)</span>'
                        }
                    </div>
                </div>
            `;

            document.getElementById('itemDetailModal').classList.remove('hidden');
        }

        function openMachineDetail(id) {
            const m = state.machines.find(item => item.id === id);
            if (!m) return;

            const modalTitle = document.getElementById('detailModalTitle');
            const modalContent = document.getElementById('detailModalContent');

            modalTitle.innerHTML = `
                <svg class="w-5 h-5 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" />
                </svg>
                <span>🚜 機具保養詳細資料</span>
            `;

            const statusInfo = getMachineStatusDetails(m);

            modalContent.innerHTML = `
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">機具名稱：</div>
                    <div class="col-span-2 text-slate-800 font-extrabold text-sm">${m.machineName}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">歸屬部門：</div>
                    <div class="col-span-2 text-slate-800 font-bold">${m.department}</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">當前累計時數：</div>
                    <div class="col-span-2 font-mono text-slate-800 font-black text-sm">${m.currentHours} 小時 (h)</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">下次保養目標：</div>
                    <div class="col-span-2 font-mono text-indigo-700 font-black text-sm">${m.nextMaintenanceHours} 小時 (h)</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">保養警報臨界：</div>
                    <div class="col-span-2 text-slate-800 font-medium">相差剩餘 ${m.warningThreshold} 小時即開始預警</div>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <div class="text-slate-400 font-bold">目前警示狀態：</div>
                    <div class="col-span-2"><span class="px-2 py-0.5 rounded border text-[10px] font-bold ${statusInfo.color}">${statusInfo.text}</span></div>
                </div>
                <div class="border-t border-slate-100 pt-3">
                    <div class="text-slate-400 font-bold mb-1">保養規格備註：</div>
                    <div class="bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-slate-600 font-medium leading-relaxed">${m.remark || '（無特別保養規格登記）'}</div>
                </div>
                <div class="border-t border-slate-100 pt-3 text-[11px] text-slate-400 flex justify-between">
                    <span>最後時數更新同仁: ${m.lastUpdatedBy}</span>
                    <span>更新時間: ${m.lastUpdatedAt}</span>
                </div>
            `;

            document.getElementById('itemDetailModal').classList.remove('hidden');
        }

        function closeDetailModal() {
            document.getElementById('itemDetailModal').classList.add('hidden');
        }

        // ===================================================
        // 管理權限解鎖邏輯
        // ===================================================
        function updateAdminUI() {
            const btn = document.getElementById('adminPermissionBtn');
            const settingsLock = document.getElementById('settingsLockOverlay');
            const employeeModalLockedMsg = document.getElementById('employeeModalLockedMsg');
            const employeeModalAddForm = document.getElementById('employeeModalAddForm');

            if (state.isAdmin) {
                // 已解鎖狀態 (管理權限)
                btn.className = "flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold bg-indigo-50 text-indigo-700 border border-indigo-200 transition shadow-sm active:scale-95";
                btn.innerHTML = `
                    <svg class="w-3.5 h-3.5 text-indigo-600" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 9.9-1"/></svg>
                    <span>🔓 管理權限 (已解鎖)</span>
                `;
                if (settingsLock) settingsLock.classList.add('hidden', 'pointer-events-none');
                if (employeeModalLockedMsg) employeeModalLockedMsg.classList.add('hidden');
                if (employeeModalAddForm) employeeModalAddForm.classList.remove('hidden');
            } else {
                // 已鎖定狀態 (管理權限)
                btn.className = "flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold bg-slate-800 text-white border border-slate-700 hover:bg-slate-900 transition shadow-sm active:scale-95";
                btn.innerHTML = `
                    <svg class="w-3.5 h-3.5 text-amber-400" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg>
                    <span>🔒 管理權限</span>
                `;
                if (settingsLock) settingsLock.classList.remove('hidden', 'pointer-events-none');
                if (employeeModalLockedMsg) employeeModalLockedMsg.classList.remove('hidden');
                if (employeeModalAddForm) employeeModalAddForm.classList.add('hidden');
            }
        }

        function toggleAdminPermission() {
            if (state.isAdmin) {
                state.isAdmin = false;
                showToast("管理權限已重新鎖定");
                saveState();
                updateAdminUI();
            } else {
                triggerAdminModal();
            }
        }

        function triggerAdminModal() {
            document.getElementById('adminPasswordInput').value = '';
            document.getElementById('adminModal').classList.remove('hidden');
            setTimeout(() => {
                document.getElementById('adminPasswordInput').focus();
            }, 150);
        }

        function closeAdminModal() {
            document.getElementById('adminModal').classList.add('hidden');
        }

        function onAdminPasswordKeydown(e) {
            if (e.key === 'Enter') {
                e.preventDefault();
                verifyAdminPassword();
            }
        }

        function verifyAdminPassword() {
            const password = document.getElementById('adminPasswordInput').value;
            if (password === '8831') {
                state.isAdmin = true;
                closeAdminModal();
                showToast("管理權限驗證成功！已開啟清冊與基礎設定編輯權限。");
                saveState();
                updateAdminUI();
            } else {
                showToast("密碼錯誤，請重新輸入！", false);
            }
        }

        function quickBypassAdmin() {
            document.getElementById('adminPasswordInput').value = '8831';
            verifyAdminPassword();
        }

        // ===================================================
        // 核心運算與過濾邏輯
        // ===================================================
        
        // 取得過期狀態樣式
        function getExpiryStatusDetails(expiryDate, completed) {
            if (completed) return { text: '已完成', color: 'bg-emerald-50 text-emerald-700 border-emerald-100 font-bold' };
            const todayStr = getTodayDateString();
            const todayTime = new Date(todayStr).getTime();
            const expiryTime = new Date(expiryDate).getTime();
            const diffDays = Math.ceil((expiryTime - todayTime) / (1000 * 60 * 60 * 24));

            if (diffDays < 0) {
                return { text: `已過期 ${Math.abs(diffDays)} 天`, color: 'bg-rose-50 text-rose-700 border-rose-100 animate-pulse font-extrabold' };
            } else if (diffDays === 0) {
                return { text: '今天到期！', color: 'bg-amber-100 text-amber-800 border-amber-300 font-black' };
            } else {
                return { text: `${diffDays} 天後到期`, color: 'bg-slate-100 text-slate-700 border-slate-200' };
            }
        }

        // 機具時數狀態樣式
        function getMachineStatusDetails(machine) {
            const current = Number(machine.currentHours);
            const target = Number(machine.nextMaintenanceHours);
            const warning = Number(machine.warningThreshold);
            const remaining = target - current;

            if (remaining <= 0) {
                return { text: `已逾期保養 (${Math.abs(remaining)}h)`, color: 'text-rose-700 bg-rose-50 border-rose-100 font-extrabold', isCritical: true };
            } else if (remaining <= warning) {
                return { text: `即將保養 (剩 ${remaining}h)`, color: 'text-amber-700 bg-amber-50 border-amber-100 font-bold', isCritical: true };
            } else {
                return { text: `正常 (剩 ${remaining}h)`, color: 'text-emerald-700 bg-emerald-50 border-emerald-100 font-medium', isCritical: false };
            }
        }

        // ===================================================
        // 界面切換與渲染主模組
        // ===================================================
        function switchTab(tabId) {
            state.activeTab = tabId;
            document.getElementById('tab-date-reminders').className = "flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50";
            document.getElementById('tab-machinery-hours').className = "flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50";
            document.getElementById('tab-settings').className = "flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50";

            document.getElementById(`tab-${tabId}`).className = "flex items-center gap-1.5 px-4.5 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 bg-emerald-600 text-white shadow-sm";

            document.getElementById('content-date-reminders').classList.add('hidden');
            document.getElementById('content-machinery-hours').classList.add('hidden');
            document.getElementById('content-settings').classList.add('hidden');

            document.getElementById(`content-${tabId}`).classList.remove('hidden');
            saveState();
        }

        function setDateFilter(filterVal) {
            state.dateFilter = filterVal;
            document.getElementById('filter-all').className = "px-3 py-1.5 rounded-lg transition-all";
            document.getElementById('filter-pending').className = "px-3 py-1.5 rounded-lg transition-all";
            document.getElementById('filter-completed').className = "px-3 py-1.5 rounded-lg transition-all";

            let activeBtnClass = "px-3 py-1.5 rounded-lg transition-all bg-white text-slate-800 shadow-sm font-extrabold";
            if (filterVal === 'pending') {
                activeBtnClass = "px-3 py-1.5 rounded-lg transition-all bg-amber-100 text-amber-800 shadow-sm font-extrabold";
            } else if (filterVal === 'completed') {
                activeBtnClass = "px-3 py-1.5 rounded-lg transition-all bg-emerald-100 text-emerald-800 shadow-sm font-extrabold";
            }
            document.getElementById(`filter-${filterVal}`).className = activeBtnClass;
            renderDateTable();
        }

        function renderAll() {
            updateAdminUI();
            renderUserDropdowns();
            renderLeftAlerts();
            renderDateTable();
            renderMachineTable();
            renderSettingsTab();
        }

        // 渲染操作人與下拉名單
        function renderUserDropdowns() {
            const selectEl = document.getElementById('currentUserSelect');
            selectEl.innerHTML = '';
            state.employees.forEach(emp => {
                const opt = document.createElement('option');
                opt.value = emp;
                opt.textContent = emp;
                if (emp === state.currentUser) {
                    opt.selected = true;
                }
                selectEl.appendChild(opt);
            });
            const manageOpt = document.createElement('option');
            manageOpt.value = '___manage___';
            manageOpt.textContent = '🛠 整理同仁名冊...';
            selectEl.appendChild(manageOpt);

            // 更新到期提醒的新增表單下拉
            const rDeptSelect = document.getElementById('r-department');
            rDeptSelect.innerHTML = '';
            state.departments.forEach(dept => {
                const opt = document.createElement('option');
                opt.value = dept;
                opt.textContent = dept;
                rDeptSelect.appendChild(opt);
            });

            const rManagerSelect = document.getElementById('r-manager');
            rManagerSelect.innerHTML = '';
            state.employees.forEach(emp => {
                const opt = document.createElement('option');
                opt.value = emp;
                opt.textContent = emp;
                rManagerSelect.appendChild(opt);
            });

            // 更新機具新增表單下拉
            const mDeptSelect = document.getElementById('m-department');
            mDeptSelect.innerHTML = '';
            state.departments.forEach(dept => {
                const opt = document.createElement('option');
                opt.value = dept;
                opt.textContent = dept;
                mDeptSelect.appendChild(opt);
            });
        }

        function onUserSelectChange(el) {
            if (el.value === '___manage___') {
                openEmployeeModalDirect();
                el.value = state.currentUser;
            } else {
                state.currentUser = el.value;
                showToast(`操作人切換為：${el.value}`);
                saveState();
            }
        }

        // 渲染左側警報面板 (待辦事項提醒 與 機具保養提醒)
        function renderLeftAlerts() {
            const todayStr = getTodayDateString();
            const todayTime = new Date(todayStr).getTime();

            // 1. 待辦事項提醒 (未完成 且 到達預警臨界點或過期)
            const activeDateReminders = state.dateReminders.filter(item => {
                if (item.completed) return false;
                const expiryTime = new Date(item.expiryDate).getTime();
                const diffDays = Math.ceil((expiryTime - todayTime) / (1000 * 60 * 60 * 24));

                const trigger1 = item.remindDays1 && diffDays <= Number(item.remindDays1);
                const trigger2 = item.remindDays2 && diffDays <= Number(item.remindDays2);
                const trigger3 = item.remindDays3 && diffDays <= Number(item.remindDays3);
                const isOverdue = diffDays < 0;

                return trigger1 || trigger2 || trigger3 || isOverdue;
            });

            document.getElementById('dateAlertCount').textContent = activeDateReminders.length;
            const dateContainer = document.getElementById('dateAlertContainer');
            dateContainer.innerHTML = '';

            if (activeDateReminders.length === 0) {
                dateContainer.innerHTML = `
                    <div class="text-center py-10 text-slate-400 text-xs flex flex-col items-center justify-center gap-2">
                        <svg class="w-8 h-8 text-emerald-500/60" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12" /></svg>
                        <div>
                            <p class="font-bold text-slate-500">目前無待辦事項提醒！</p>
                            <p class="text-[10px] text-slate-400">所有日期品項均已結案</p>
                        </div>
                    </div>
                `;
            } else {
                activeDateReminders.forEach(item => {
                    const status = getExpiryStatusDetails(item.expiryDate, item.completed);
                    const div = document.createElement('div');
                    div.className = "p-3 bg-slate-50/60 rounded-xl border border-slate-200/50 hover:border-amber-400/60 hover:bg-slate-100/50 cursor-pointer transition flex flex-col gap-1.5";
                    div.setAttribute('onclick', `openDateItemDetail('${item.id}')`);
                    div.innerHTML = `
                        <div class="flex items-start justify-between gap-2">
                            <span class="text-xs font-bold text-slate-800 line-clamp-2">${item.itemName}</span>
                            <button 
                                onclick="event.stopPropagation(); toggleDateReminder('${item.id}')"
                                class="flex-shrink-0 p-1 rounded-lg bg-emerald-50 hover:bg-emerald-500 text-emerald-600 hover:text-white transition shadow-sm"
                                title="點擊快速標記為已完成"
                            >
                                <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><polyline points="20 6 9 17 4 12" /></svg>
                            </button>
                        </div>
                        <div class="flex flex-wrap items-center justify-between gap-1 text-[10px]">
                            <span class="px-2 py-0.5 rounded-md border text-[9px] ${status.color}">${status.text}</span>
                            <span class="text-slate-400 font-mono">限期：${item.expiryDate}</span>
                        </div>
                    `;
                    dateContainer.appendChild(div);
                });
            }

            // 2. 機具保養提醒 (剩餘時數 <= 警報閾值)
            const activeMachineAlerts = state.machines.filter(m => {
                const remaining = Number(m.nextMaintenanceHours) - Number(m.currentHours);
                return remaining <= Number(m.warningThreshold);
            });

            document.getElementById('machineAlertCount').textContent = activeMachineAlerts.length;
            const machineContainer = document.getElementById('machineAlertContainer');
            machineContainer.innerHTML = '';

            if (activeMachineAlerts.length === 0) {
                machineContainer.innerHTML = `
                    <div class="text-center py-10 text-slate-400 text-xs flex flex-col items-center justify-center gap-2">
                        <svg class="w-8 h-8 text-emerald-500/60" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12" /></svg>
                        <div>
                            <p class="font-bold text-slate-500">目前無機具保養提醒！</p>
                            <p class="text-[10px] text-slate-400">所有設備時數均正常</p>
                        </div>
                    </div>
                `;
            } else {
                activeMachineAlerts.forEach(m => {
                    const remaining = Number(m.nextMaintenanceHours) - Number(m.currentHours);
                    const progressPct = Math.min(100, Math.max(0, (Number(m.currentHours) / Number(m.nextMaintenanceHours)) * 100));
                    const isOverdue = remaining <= 0;

                    const div = document.createElement('div');
                    div.className = `p-3.5 rounded-xl border transition cursor-pointer flex flex-col gap-2 ${isOverdue ? 'bg-rose-50/55 border-rose-200 hover:bg-rose-100/40' : 'bg-amber-50/45 border-amber-200 hover:bg-amber-100/40'}`;
                    div.setAttribute('onclick', `openMachineDetail('${m.id}')`);
                    div.innerHTML = `
                        <div class="flex items-start justify-between gap-1">
                            <div>
                                <h3 class="text-xs font-bold text-slate-800 flex items-center gap-1.5 flex-wrap">
                                    <span class="line-clamp-2">${m.machineName}</span>
                                </h3>
                                <p class="text-[10px] text-slate-400 mt-1 font-semibold">歸屬部門: ${m.department}</p>
                            </div>
                            <button
                                onclick="event.stopPropagation(); openMaintCompleteModal('${m.id}')"
                                class="text-[10px] font-black bg-emerald-600 text-white px-2.5 py-1 rounded-lg hover:bg-emerald-700 transition flex-shrink-0 shadow-sm whitespace-nowrap"
                            >
                                保養完成
                            </button>
                        </div>
                        <div>
                            <div class="flex justify-between text-[10px] font-mono font-bold text-slate-500 mb-1">
                                <span>目前: ${m.currentHours}h</span>
                                <span class="${isOverdue ? 'text-rose-600' : 'text-amber-700'}">目標: ${m.nextMaintenanceHours}h</span>
                            </div>
                            <div class="w-full h-1.5 bg-slate-200/80 rounded-full overflow-hidden">
                                <div class="h-full rounded-full ${isOverdue ? 'bg-rose-500' : 'bg-amber-500'}" style="width: ${progressPct}%"></div>
                            </div>
                        </div>
                        <div class="flex items-center justify-between text-[9px] text-slate-400 font-bold">
                            <span class="${isOverdue ? 'text-rose-600 font-black' : 'text-amber-700'}">
                                ${isOverdue ? `超時 ${Math.abs(remaining)} 小時!` : `剩餘 ${remaining} 小時保養`}
                            </span>
                            <span>回報人: ${m.lastUpdatedBy}</span>
                        </div>
                    `;
                    machineContainer.appendChild(div);
                });
            }
        }

        // 渲染日期提醒工作台表格
        function renderDateTable() {
            const tbody = document.getElementById('dateReminderTableBody');
            tbody.innerHTML = '';

            const q = document.getElementById('searchQuery').value.toLowerCase().trim();

            const filtered = state.dateReminders.filter(item => {
                if (state.dateFilter === 'pending' && item.completed) return false;
                if (state.dateFilter === 'completed' && !item.completed) return false;

                if (q) {
                    const matchName = item.itemName.toLowerCase().includes(q);
                    const matchDept = item.department.toLowerCase().includes(q);
                    const matchManager = item.manager.toLowerCase().includes(q);
                    return matchName || matchDept || matchManager;
                }
                return true;
            });

            if (filtered.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colSpan="7" class="text-center py-12 text-slate-400 font-bold">
                            目前無符合篩選條件的提醒項目
                        </td>
                    </tr>
                `;
                return;
            }

            filtered.forEach(item => {
                const status = getExpiryStatusDetails(item.expiryDate, item.completed);
                const tr = document.createElement('tr');
                tr.className = `hover:bg-slate-50 transition-all ${item.completed ? 'opacity-65 bg-slate-50/30' : ''}`;
                
                // 刪除按鈕權限管控樣式 (維持最高管理保護)
                const deleteActionHtml = state.isAdmin 
                    ? `<button onclick="event.stopPropagation(); deleteDateReminder('${item.id}', '${item.itemName}')" class="p-1.5 hover:bg-rose-50 text-slate-400 hover:text-rose-500 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6" /><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" /></svg></button>`
                    : `<button onclick="event.stopPropagation(); triggerAdminModal()" class="p-1.5 text-slate-300 hover:text-slate-500 rounded-lg transition" title="需要管理權限以進行項目刪除"><svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg></button>`;

                // 點擊列任一資訊儲存格以查看完整資訊
                const clickAttr = `onclick="openDateItemDetail('${item.id}')"`;

                tr.innerHTML = `
                    <td class="px-2 py-4 text-center">
                        <div class="flex items-center justify-center">
                            <button
                                onclick="event.stopPropagation(); toggleDateReminder('${item.id}')"
                                class="w-5.5 h-5.5 rounded-lg border-2 flex items-center justify-center transition-all ${item.completed ? 'bg-emerald-600 border-emerald-600 text-white' : 'border-slate-300 hover:border-slate-400 hover:bg-slate-50'}"
                            >
                                ${item.completed ? '<svg class="w-3.5 h-3.5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><polyline points="20 6 9 17 4 12" /></svg>' : ''}
                            </button>
                        </div>
                    </td>
                    <td ${clickAttr} class="px-2 py-4 text-center cursor-pointer hover:bg-slate-100/50 transition">
                        <span class="bg-slate-100 text-slate-600 border border-slate-200/60 px-2.5 py-0.5 rounded-md font-bold text-[10px] inline-block whitespace-nowrap">
                            ${item.department}
                        </span>
                    </td>
                    <td ${clickAttr} class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition">
                        <div class="text-xs font-extrabold text-slate-800 line-clamp-2 ${item.completed ? 'line-through text-slate-400' : ''}">
                            ${item.itemName}
                        </div>
                        ${item.remark ? `<div class="text-[10px] text-slate-400 font-medium italic mt-0.5 line-clamp-1">備註: ${item.remark}</div>` : ''}
                        ${item.completed ? `<div class="text-[9px] text-emerald-600 font-bold mt-1 flex items-center gap-1">✓ 已結案</div>` : ''}
                    </td>
                    <td ${clickAttr} class="px-4 py-4 text-slate-700 font-semibold cursor-pointer hover:bg-slate-100/50 transition">${item.manager}</td>
                    <td ${clickAttr} class="px-4 py-4 font-mono font-bold text-slate-700 cursor-pointer hover:bg-slate-100/50 transition">${item.expiryDate}</td>
                    <td ${clickAttr} class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition">
                        <span class="px-2 py-0.5 rounded border text-[10px] ${status.color} whitespace-nowrap inline-block">${status.text}</span>
                    </td>
                    <td class="px-4 py-4 text-center">
                        ${deleteActionHtml}
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        // 渲染機具表格
        function renderMachineTable() {
            const tbody = document.getElementById('machineTableBody');
            tbody.innerHTML = '';

            if (state.machines.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colSpan="6" class="text-center py-12 text-slate-400 font-bold">
                            目前無時數追蹤機具，請於上方建檔
                        </td>
                    </tr>
                `;
                return;
            }

            state.machines.forEach(m => {
                const status = getMachineStatusDetails(m);
                const progressPct = Math.min(100, Math.max(0, (Number(m.currentHours) / Number(m.nextMaintenanceHours)) * 100));

                const deleteActionHtml = state.isAdmin 
                    ? `<button onclick="event.stopPropagation(); deleteMachine('${m.id}', '${m.machineName}')" class="p-1.5 hover:bg-rose-50 text-slate-400 hover:text-rose-500 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6" /><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" /></svg></button>`
                    : `<button onclick="event.stopPropagation(); triggerAdminModal()" class="p-1.5 text-slate-300 hover:text-slate-500 rounded-lg transition" title="需要管理權限以刪除機具"><svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg></button>`;

                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-all";
                
                // 點擊列任一資訊儲存格以查看完整資訊
                const clickAttr = `onclick="openMachineDetail('${m.id}')"`;

                tr.innerHTML = `
                    <!-- 欄位 1: 部門 (w-24) -->
                    <td ${clickAttr} class="px-4 py-4 text-center cursor-pointer hover:bg-slate-100/50 transition">
                        <span class="bg-slate-100 text-slate-600 border border-slate-200/60 px-2 py-0.5 rounded-md font-bold text-[10px] inline-block whitespace-nowrap">
                            ${m.department}
                        </span>
                    </td>

                    <!-- 欄位 2: 機具名稱 (Fluid, line-clamp-2 完美限制最多雙列) -->
                    <td ${clickAttr} class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition">
                        <div class="font-extrabold text-slate-800 text-xs line-clamp-2">${m.machineName}</div>
                    </td>

                    <!-- 欄位 3: 保養時數 / 備註 (縮減寬度 w-48) -->
                    <td ${clickAttr} class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition">
                        <div class="flex items-center justify-between text-[10px] font-mono font-bold text-slate-500 mb-1">
                            <span>當前: <span class="text-slate-800 font-black">${m.currentHours}h</span></span>
                            <span>目標: <span class="text-indigo-600 font-black">${m.nextMaintenanceHours}h</span></span>
                        </div>
                        <div class="w-full h-1.5 bg-slate-200/80 rounded-full overflow-hidden mb-1">
                            <div class="h-full rounded-full transition-all duration-300 ${status.isCritical ? 'bg-amber-500' : 'bg-emerald-500'}" style="width: ${progressPct}%"></div>
                        </div>
                        ${m.remark ? `<div class="text-[10px] text-slate-400 font-medium italic line-clamp-1">備註: ${m.remark}</div>` : ''}
                    </td>

                    <!-- 欄位 4: 警示狀態 (w-32) -->
                    <td ${clickAttr} class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition">
                        <span class="px-2 py-0.5 rounded border text-[10px] block text-center ${status.color} whitespace-nowrap">
                            ${status.text}
                        </span>
                    </td>

                    <!-- 欄位 5: 現場時數登錄 (w-36) -->
                    <td class="px-4 py-4 text-center">
                        <div class="flex justify-center gap-1.5">
                            <button
                                onclick="openSingleHoursLog('${m.id}', ${m.currentHours})"
                                class="bg-amber-500 hover:bg-amber-600 text-white px-2.5 py-1 rounded-lg text-[10px] font-bold whitespace-nowrap shadow-sm active:scale-95 transition"
                            >
                                登錄
                            </button>
                            <button
                                onclick="openMaintCompleteModal('${m.id}')"
                                class="bg-emerald-600 hover:bg-emerald-700 text-white px-2.5 py-1 rounded-lg text-[10px] font-bold whitespace-nowrap shadow-sm active:scale-95 transition"
                            >
                                完工
                            </button>
                        </div>
                    </td>

                    <!-- 欄位 6: 刪除 -->
                    <td class="px-4 py-4 text-center">
                        ${deleteActionHtml}
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        // 渲染基礎設定分頁 (受到管理權限鎖定或解鎖影響)
        function renderSettingsTab() {
            // 部門列表
            const deptContainer = document.getElementById('settingsDeptList');
            deptContainer.innerHTML = '';
            state.departments.forEach(dept => {
                const div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                
                const deleteActionHtml = state.isAdmin
                    ? `<button onclick="deleteDepartment('${dept}')" class="text-rose-500 hover:bg-rose-50 p-1 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6" /><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" /></svg></button>`
                    : ``;

                div.innerHTML = `
                    <span class="text-xs font-bold text-slate-700">${dept}</span>
                    ${deleteActionHtml}
                `;
                deptContainer.appendChild(div);
            });

            // 員工列表
            const empContainer = document.getElementById('settingsEmpList');
            empContainer.innerHTML = '';
            state.employees.forEach(emp => {
                const div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                
                const deleteActionHtml = state.isAdmin
                    ? `<button onclick="deleteEmployee('${emp}')" class="text-rose-500 hover:bg-rose-50 p-1 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6" /><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" /></svg></button>`
                    : ``;

                div.innerHTML = `
                    <span class="text-xs font-bold text-slate-700 flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-slate-400"></span>
                        ${emp}
                    </span>
                    ${deleteActionHtml}
                `;
                empContainer.appendChild(div);
            });
        }

        // ===================================================
        // 核心表單操作與邏輯事件 (開放給每個人)
        // ===================================================
        
        // 1. 日期提醒新增與勾選 (開放給每個人)
        function addDateReminder(e) {
            e.preventDefault();

            const itemName = document.getElementById('r-itemName').value.trim();
            const department = document.getElementById('r-department').value;
            const manager = document.getElementById('r-manager').value;
            const expiryDate = document.getElementById('r-expiryDate').value;
            const remindDays1 = document.getElementById('r-remindDays1').value;
            const remindDays2 = document.getElementById('r-remindDays2').value;
            const remindDays3 = document.getElementById('r-remindDays3').value;
            const remark = document.getElementById('r-remark').value.trim();

            if (!itemName || !expiryDate) {
                showToast("請確實填寫品項名稱與到期日期！", false);
                return;
            }

            const newReminder = {
                id: 'local-r-' + Date.now(),
                itemName,
                department,
                manager,
                expiryDate,
                remindDays1: remindDays1 ? Number(remindDays1) : 0,
                remindDays2: remindDays2 ? Number(remindDays2) : 0,
                remindDays3: remindDays3 ? Number(remindDays3) : 0,
                remark,
                completed: false,
                completedBy: '',
                completedAt: ''
            };

            state.dateReminders.push(newReminder);
            state.dateReminders.sort((a, b) => new Date(a.expiryDate) - new Date(b.expiryDate));

            document.getElementById('r-itemName').value = '';
            document.getElementById('r-remark').value = '';
            document.getElementById('r-expiryDate').value = '';

            showToast(`已建立提醒事項「${itemName}」`);
            saveState();
        }

        function toggleDateReminder(id) {
            const item = state.dateReminders.find(r => r.id === id);
            if (!item) return;

            item.completed = !item.completed;
            item.completedBy = item.completed ? state.currentUser : '';
            item.completedAt = item.completed ? new Date().toLocaleString('zh-TW', { timeZone: 'Asia/Taipei' }) : '';

            showToast(item.completed ? "事項已標記為【已完成】" : "事項已還原為【未完成】");
            saveState();
        }

        function deleteDateReminder(id, name) {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            triggerConfirm(`確定要永久刪除「${name}」嗎？此操作將無法還原。`, () => {
                state.dateReminders = state.dateReminders.filter(r => r.id !== id);
                showToast("項目已刪除完成");
                saveState();
            });
        }

        // 2. 機具新增 (開放給每個人)
        function addMachine(e) {
            e.preventDefault();

            const machineName = document.getElementById('m-machineName').value.trim();
            const department = document.getElementById('m-department').value;
            const currentHours = document.getElementById('m-currentHours').value;
            const nextMaintenanceHours = document.getElementById('m-nextMaintenanceHours').value;
            const warningThreshold = document.getElementById('m-warningThreshold').value;
            const remark = document.getElementById('m-remark').value.trim();

            if (!machineName) {
                showToast("請確實填寫機具名稱！", false);
                return;
            }

            const newMachine = {
                id: 'local-m-' + Date.now(),
                machineNo: '',
                machineName,
                department,
                currentHours: Number(currentHours) || 0,
                nextMaintenanceHours: Number(nextMaintenanceHours) || 250,
                warningThreshold: Number(warningThreshold) || 50,
                remark,
                lastUpdatedBy: state.currentUser,
                lastUpdatedAt: new Date().toLocaleString('zh-TW', { timeZone: 'Asia/Taipei' })
            };

            state.machines.push(newMachine);
            state.machines.sort((a, b) => a.machineName.localeCompare(b.machineName));

            document.getElementById('m-machineName').value = '';
            document.getElementById('m-currentHours').value = '0';
            document.getElementById('m-nextMaintenanceHours').value = '250';
            document.getElementById('m-warningThreshold').value = '50';
            document.getElementById('m-remark').value = '';

            showToast(`機具「${machineName}」已登錄成功！`);
            saveState();
        }

        function deleteMachine(id, name) {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            triggerConfirm(`確定要刪除「${name}」時數追蹤項目嗎？所有保養數據將被清除。`, () => {
                state.machines = state.machines.filter(m => m.id !== id);
                showToast("機具檔案已永久刪除");
                saveState();
            });
        }

        // ===================================================
        // 師傅時數快速登錄與保養結案視窗
        // ===================================================
        function openHoursLogModal() {
            if (state.machines.length === 0) {
                showToast("目前尚無機具檔案，請先新增機具！", false);
                return;
            }

            // 載入機具下拉
            const select = document.getElementById('logMachineSelect');
            select.innerHTML = '';
            state.machines.forEach(m => {
                const opt = document.createElement('option');
                opt.value = m.id;
                opt.textContent = `${m.machineName} (目前: ${m.currentHours}h)`;
                select.appendChild(opt);
            });

            // 預設填入第一個機具的目前時數
            document.getElementById('logHoursInput').value = state.machines[0].currentHours;

            // 載入操作人下拉
            const opSelect = document.getElementById('logOperatorSelect');
            opSelect.innerHTML = '';
            state.employees.forEach(emp => {
                const opt = document.createElement('option');
                opt.value = emp;
                opt.textContent = emp;
                if (emp === state.currentUser) {
                    opt.selected = true;
                }
                opSelect.appendChild(opt);
            });

            document.getElementById('hoursLogModal').classList.remove('hidden');
        }

        function openSingleHoursLog(machineId, currentHours) {
            openHoursLogModal();
            document.getElementById('logMachineSelect').value = machineId;
            document.getElementById('logHoursInput').value = currentHours;
        }

        function closeHoursLogModal() {
            document.getElementById('hoursLogModal').classList.add('hidden');
        }

        function submitHoursLog(e) {
            e.preventDefault();
            const machineId = document.getElementById('logMachineSelect').value;
            const inputHours = Number(document.getElementById('logHoursInput').value);
            const operator = document.getElementById('logOperatorSelect').value;

            if (isNaN(inputHours) || inputHours < 0) {
                showToast("請輸入合理的總累計時數！", false);
                return;
            }

            const m = state.machines.find(item => item.id === machineId);
            if (!m) return;

            m.currentHours = inputHours;
            m.lastUpdatedBy = operator;
            m.lastUpdatedAt = new Date().toLocaleString('zh-TW', { timeZone: 'Asia/Taipei' });

            closeHoursLogModal();
            showToast(`目前時數更新完成！已同步完成保養稽核。`);
            saveState();
        }

        // 保養完成結案
        function openMaintCompleteModal(machineId) {
            const m = state.machines.find(item => item.id === machineId);
            if (!m) return;

            document.getElementById('maintMachineId').value = machineId;
            document.getElementById('maintNextHoursInput').value = m.currentHours + 250;
            document.getElementById('maintCompleteModal').classList.remove('hidden');
        }

        function closeMaintCompleteModal() {
            document.getElementById('maintCompleteModal').classList.add('hidden');
        }

        function submitMaintComplete(e) {
            e.preventDefault();
            const machineId = document.getElementById('maintMachineId').value;
            const nextHours = Number(document.getElementById('maintNextHoursInput').value);

            if (isNaN(nextHours) || nextHours <= 0) {
                showToast("保養目標時數輸入不合理！", false);
                return;
            }

            const m = state.machines.find(item => item.id === machineId);
            if (!m) return;

            m.nextMaintenanceHours = nextHours;
            m.lastUpdatedBy = state.currentUser;
            m.lastUpdatedAt = new Date().toLocaleString('zh-TW', { timeZone: 'Asia/Taipei' });

            closeMaintCompleteModal();
            showToast(`機具保養完工結案確認！已重設警示週期。`);
            saveState();
        }

        // ===================================================
        // 基礎設定名單增刪 (部門、同仁名冊 - 均需管理權限驗證)
        // ===================================================
        function addDepartment() {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            const name = document.getElementById('newDeptName').value.trim();
            if (!name) return;
            if (state.departments.includes(name)) {
                showToast("此部門名稱已存在！", false);
                return;
            }
            state.departments.push(name);
            document.getElementById('newDeptName').value = '';
            showToast(`已新增部門分類「${name}」`);
            saveState();
        }

        function deleteDepartment(name) {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            if (state.departments.length <= 1) {
                showToast("必須保留至少一個部門分類！", false);
                return;
            }
            triggerConfirm(`確定要刪除部門分類「${name}」嗎？`, () => {
                state.departments = state.departments.filter(d => d !== name);
                showToast("部門分類已移除");
                saveState();
            });
        }

        function addEmployee() {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            const name = document.getElementById('newEmployeeName').value.trim();
            if (!name) return;
            if (state.employees.includes(name)) {
                showToast("此同仁姓名已存在！", false);
                return;
            }
            state.employees.push(name);
            document.getElementById('newEmployeeName').value = '';
            showToast(`同仁「${name}」已成功加入`);
            saveState();
        }

        function deleteEmployee(name) {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            if (state.employees.length <= 1) {
                showToast("名冊中必須保留至少一位同仁！", false);
                return;
            }
            triggerConfirm(`確定要將「${name}」從同仁名冊中移除嗎？`, () => {
                state.employees = state.employees.filter(e => e !== name);
                if (state.currentUser === name) {
                    state.currentUser = state.employees[0];
                }
                showToast("同仁已自名冊移除");
                saveState();
            });
        }

        // 同仁管理快捷視窗
        function openEmployeeModalDirect() {
            renderModalEmployeeList();
            document.getElementById('employeeModal').classList.remove('hidden');
        }

        function closeEmployeeModal() {
            document.getElementById('employeeModal').classList.add('hidden');
        }

        function renderModalEmployeeList() {
            const list = document.getElementById('modalEmpList');
            list.innerHTML = '';
            state.employees.forEach(emp => {
                const div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                
                const deleteActionHtml = state.isAdmin 
                    ? `<button onclick="deleteEmployeeFromModal('${emp}')" class="text-rose-500 hover:bg-rose-50 p-1 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6" /><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" /></svg></button>`
                    : ``;

                div.innerHTML = `
                    <span class="text-xs font-bold text-slate-700">${emp}</span>
                    ${deleteActionHtml}
                `;
                list.appendChild(div);
            });
        }

        function addEmployeeFromModal() {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            const nameInput = document.getElementById('modalEmployeeName');
            const name = nameInput.value.trim();
            if (!name) return;
            if (state.employees.includes(name)) {
                showToast("此同仁姓名已存在！", false);
                return;
            }
            state.employees.push(name);
            nameInput.value = '';
            showToast(`同仁「${name}」已成功加入`);
            saveState();
            renderModalEmployeeList();
        }

        function deleteEmployeeFromModal(name) {
            if (!state.isAdmin) {
                triggerAdminModal();
                return;
            }
            if (state.employees.length <= 1) {
                showToast("名冊中必須保留至少一位同仁！", false);
                return;
            }
            triggerConfirm(`確定要將「${name}」從同仁名冊中移除嗎？`, () => {
                state.employees = state.employees.filter(e => e !== name);
                if (state.currentUser === name) {
                    state.currentUser = state.employees[0];
                }
                showToast("同仁已自名冊移除");
                saveState();
                renderModalEmployeeList();
            });
        }

        // ===================================================
        // 初始化載入
        // ===================================================
        window.onload = function () {
            loadState();
            renderAll();
        }
    </script>
</body>
</html>
