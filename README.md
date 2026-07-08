<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <base target="_top">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;900&display=swap');
        body { font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Microsoft JhengHei", sans-serif; }
        @keyframes subtle-float { 0%,100%{transform:translateY(0);} 50%{transform:translateY(-4px);} }
        .float-subtle { animation: subtle-float 3s ease-in-out infinite; }
        @keyframes scaleIn { from{transform:scale(0.96);opacity:0;} to{transform:scale(1);opacity:1;} }
        .animate-scaleIn { animation: scaleIn 0.15s ease-out; }
        .custom-scrollbar::-webkit-scrollbar { width:6px; height:6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background:#cbd5e1; border-radius:10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background:#94a3b8; }
        .line-clamp-2 { display:-webkit-box; -webkit-line-clamp:2; -webkit-box-orient:vertical; overflow:hidden; }
        .line-clamp-1 { display:-webkit-box; -webkit-line-clamp:1; -webkit-box-orient:vertical; overflow:hidden; }
    </style>
</head>
<body class="bg-slate-50 text-slate-700 min-h-screen flex flex-col antialiased">

    <div id="appLoading" class="fixed inset-0 z-[90] bg-slate-50 flex items-center justify-center">
        <div class="flex flex-col items-center gap-3 text-slate-400">
            <svg class="w-8 h-8 animate-spin text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M21 12a9 9 0 1 1-6.219-8.56" stroke-linecap="round"/></svg>
            <p class="text-xs font-bold">連線試算表資料中…</p>
        </div>
    </div>

    <div id="appRoot" class="flex-1 flex flex-col hidden">

    <header class="bg-white border-b border-slate-200/80 sticky top-0 z-40 px-6 py-4 shadow-sm">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
            <div class="flex items-center gap-3">
                <div class="p-2.5 bg-emerald-50 text-emerald-600 rounded-2xl border border-emerald-100 shadow-sm">
                    <svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" /></svg>
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-tight text-slate-800 flex items-center gap-2">追蹤管理系統<span class="text-[9px] font-black text-emerald-600 bg-emerald-50 border border-emerald-100 px-1.5 py-0.5 rounded-md">雲端同步</span></h1>
                    <p class="text-xs text-slate-400 mt-0.5 font-medium">雙軌即時監控：日期合規到期與現場機具保養時數</p>
                </div>
            </div>
            <div class="flex items-center flex-wrap gap-3">
                <button onclick="openHoursLogModal()" class="flex items-center gap-2 bg-gradient-to-r from-amber-500 to-orange-500 hover:from-amber-600 hover:to-orange-600 text-white font-extrabold px-4 py-2.5 rounded-2xl transition shadow-sm active:scale-95 text-xs">
                    <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10" /><polyline points="12 6 12 12 16 14" /></svg><span>🛠 時數登錄</span>
                </button>
                <button onclick="exportBackup()" title="匯出 JSON 備份" class="flex items-center gap-1.5 px-3 py-2.5 rounded-2xl text-xs font-extrabold border border-slate-200 bg-white text-slate-600 hover:bg-slate-50 transition shadow-sm">
                    <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg><span>備份</span>
                </button>
                <button id="adminPermissionBtn" onclick="toggleAdminPermission()" class="flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold border transition shadow-sm"></button>
                <div class="flex items-center bg-slate-100 border border-slate-200 rounded-2xl p-1 gap-1">
                    <span class="text-xs text-slate-400 pl-2.5 pr-1 flex items-center gap-1 font-semibold">
                        <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" /><circle cx="12" cy="7" r="4" /></svg> 操作人:
                    </span>
                    <select id="currentUserSelect" onchange="onUserSelectChange(this)" class="bg-transparent text-xs text-slate-800 font-bold py-1 px-2.5 rounded-xl border-0 focus:ring-0 cursor-pointer focus:outline-none"></select>
                </div>
                <span id="loginAccountLabel" class="text-[11px] text-slate-400 font-bold max-w-[160px] truncate"></span>
            </div>
        </div>
    </header>

    <div class="flex-1 max-w-7xl w-full mx-auto p-6 grid grid-cols-1 lg:grid-cols-12 gap-6">
        <section class="lg:col-span-4 flex flex-col gap-6">
            <div class="bg-white rounded-2xl border border-slate-200/60 p-5 shadow-sm flex flex-col max-h-[380px]">
                <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-3.5">
                    <h2 class="font-extrabold text-sm text-slate-800 flex items-center gap-2">
                        <svg class="w-4 h-4 text-amber-500 float-subtle" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9" /><path d="M13.73 21a2 2 0 0 1-3.46 0" /></svg>
                        待辦事項提醒 <span id="dateAlertCount" class="text-amber-600 bg-amber-50 px-2 py-0.5 rounded-full text-xs font-mono ml-1">0</span>
                    </h2>
                </div>
                <div id="dateAlertContainer" class="flex-1 overflow-y-auto space-y-2.5 pr-1 custom-scrollbar"></div>
            </div>
            <div class="bg-white rounded-2xl border border-slate-200/60 p-5 shadow-sm flex flex-col max-h-[380px]">
                <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-3.5">
                    <h2 class="font-extrabold text-sm text-slate-800 flex items-center gap-2">
                        <svg class="w-4 h-4 text-rose-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" /></svg>
                        機具保養提醒 <span id="machineAlertCount" class="text-rose-600 bg-rose-50 px-2 py-0.5 rounded-full text-xs font-mono ml-1">0</span>
                    </h2>
                </div>
                <div id="machineAlertContainer" class="flex-1 overflow-y-auto space-y-2.5 pr-1 custom-scrollbar"></div>
            </div>
        </section>

        <main class="lg:col-span-8 flex flex-col gap-6">
            <div class="flex items-center justify-between bg-white p-2 rounded-2xl border border-slate-200/60 shadow-sm">
                <div class="flex flex-wrap gap-1">
                    <button id="tab-date-reminders" onclick="switchTab('date-reminders')" class="flex items-center gap-1.5 px-4 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 bg-emerald-600 text-white shadow-sm">
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2" /><line x1="16" y1="2" x2="16" y2="6" /><line x1="8" y1="2" x2="8" y2="6" /><line x1="3" y1="10" x2="21" y2="10" /></svg><span>📅 待辦事項設定</span>
                    </button>
                    <button id="tab-machinery-hours" onclick="switchTab('machinery-hours')" class="flex items-center gap-1.5 px-4 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50">
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" /></svg><span>🚜 機具保養清冊</span>
                    </button>
                    <button id="tab-settings" onclick="switchTab('settings')" class="flex items-center gap-1.5 px-4 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50">
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="4" y1="21" x2="4" y2="14" /><line x1="4" y1="10" x2="4" y2="3" /><line x1="12" y1="21" x2="12" y2="12" /><line x1="12" y1="8" x2="12" y2="3" /><line x1="20" y1="21" x2="20" y2="14" /><line x1="20" y1="12" x2="20" y2="3" /><line x1="1" y1="14" x2="7" y2="14" /><line x1="9" y1="8" x2="15" y2="8" /><line x1="17" y1="16" x2="23" y2="16" /></svg><span>⚙️ 基礎清冊管理</span>
                    </button>
                </div>
            </div>

            <div id="content-date-reminders" class="space-y-6">
                <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                    <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2"><svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19" /><line x1="5" y1="12" x2="19" y2="12" /></svg>建立新到期通知品項</h3>
                    <form onsubmit="addDateReminder(event)" class="grid grid-cols-1 md:grid-cols-12 gap-4">
                        <div class="md:col-span-8"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">1. 品項名稱 *</label><input id="r-itemName" type="text" placeholder="例如：消防合格證更換、防汛砂包定期檢查..." class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none focus:ring-1 focus:ring-emerald-500 focus:bg-white transition font-medium" required /></div>
                        <div class="md:col-span-4"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">2. 部門歸屬</label><select id="r-department" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none font-medium"></select></div>
                        <div class="md:col-span-4"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">3. 擔當同仁</label><select id="r-manager" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none font-medium"></select></div>
                        <div class="md:col-span-4"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">4. 到期日期 *</label><input id="r-expiryDate" type="date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none font-medium" required /></div>
                        <div class="md:col-span-4"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">5. 提醒警示天數 (三個設定)</label><div class="grid grid-cols-3 gap-1.5">
                            <input id="r-remindDays1" type="number" value="30" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                            <input id="r-remindDays2" type="number" value="14" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                            <input id="r-remindDays3" type="number" value="7" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-1 py-2 text-xs text-center text-slate-800 font-medium" />
                        </div></div>
                        <div class="md:col-span-9"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">6. 備註說明</label><input id="r-remark" type="text" placeholder="選填..." class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium" /></div>
                        <div class="md:col-span-3 flex items-end"><button type="submit" class="w-full bg-slate-800 hover:bg-slate-950 text-white font-extrabold py-2 px-4 rounded-xl text-xs shadow-sm transition">新增此待辦品項</button></div>
                    </form>
                </div>
                <div class="bg-white rounded-2xl border border-slate-200/60 shadow-sm overflow-hidden">
                    <div class="p-4 bg-slate-50/70 border-b border-slate-200/60 flex flex-col sm:flex-row justify-between items-center gap-3">
                        <div class="relative w-full sm:w-72">
                            <svg class="absolute left-3 top-2.5 w-3.5 h-3.5 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8" /><line x1="21" y1="21" x2="16.65" y2="16.65" /></svg>
                            <input id="searchQuery" type="text" oninput="renderDateTable()" placeholder="搜尋品項、部門、負責人..." class="w-full pl-8 pr-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs focus:outline-none font-medium" />
                        </div>
                        <div class="flex bg-slate-200/50 border border-slate-200 p-0.5 rounded-xl text-xs shrink-0 font-bold">
                            <button onclick="setDateFilter('all')" id="filter-all" class="px-3 py-1.5 rounded-lg transition-all">全部</button>
                            <button onclick="setDateFilter('pending')" id="filter-pending" class="px-3 py-1.5 rounded-lg transition-all bg-white text-slate-800 shadow-sm font-extrabold">未完成</button>
                            <button onclick="setDateFilter('completed')" id="filter-completed" class="px-3 py-1.5 rounded-lg transition-all">已完成</button>
                        </div>
                    </div>
                    <div class="overflow-x-auto custom-scrollbar">
                        <table class="w-full text-left text-xs table-fixed min-w-[850px]">
                            <thead><tr class="bg-slate-50 border-b border-slate-200 text-slate-500 font-bold uppercase"><th class="px-4 py-3 w-16 text-center">狀態</th><th class="px-4 py-3 w-28 text-center">部門</th><th class="px-4 py-3">品項 / 備註</th><th class="px-4 py-3 w-28">擔當同仁</th><th class="px-4 py-3 w-36">到期日期</th><th class="px-4 py-3 w-40">警示狀態</th><th class="px-4 py-3 w-14 text-center">刪除</th></tr></thead>
                            <tbody id="dateReminderTableBody" class="divide-y divide-slate-100 font-semibold text-slate-600"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <div id="content-machinery-hours" class="space-y-6 hidden">
                <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                    <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2"><svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19" /><line x1="5" y1="12" x2="19" y2="12" /></svg>新增時數追蹤機具</h3>
                    <form onsubmit="addMachine(event)" class="grid grid-cols-1 md:grid-cols-12 gap-4">
                        <div class="md:col-span-8"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">機具名稱 *</label><input id="m-machineName" type="text" placeholder="如: 大型挖土機, 250KV發電機" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium" required /></div>
                        <div class="md:col-span-4"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">歸屬部門</label><select id="m-department" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium"></select></div>
                        <div class="md:col-span-3"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">初始累計時數 (h)</label><input id="m-currentHours" type="number" min="0" value="0" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium" /></div>
                        <div class="md:col-span-3"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">下次保養目標時數 (h)</label><input id="m-nextMaintenanceHours" type="number" min="1" value="250" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium" /></div>
                        <div class="md:col-span-3"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">警示臨界時數 (h) ⚠️</label><input id="m-warningThreshold" type="number" min="1" value="50" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-center text-slate-800 font-medium" /></div>
                        <div class="md:col-span-3 flex items-end"><button type="submit" class="w-full bg-slate-800 hover:bg-slate-950 text-white font-extrabold py-2 px-4 rounded-xl text-xs transition">建立機具檔案</button></div>
                        <div class="md:col-span-12"><label class="block text-[11px] font-bold text-slate-500 mb-1.5">保養規格備註說明</label><input id="m-remark" type="text" placeholder="選填..." class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-medium" /></div>
                    </form>
                </div>
                <div class="bg-white rounded-2xl border border-slate-200/60 shadow-sm overflow-hidden">
                    <div class="p-4 bg-slate-50/70 border-b border-slate-200/60 flex items-center gap-2"><svg class="w-4 h-4 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z" /></svg><h3 class="font-extrabold text-slate-800 text-xs">機具保養清冊</h3></div>
                    <div class="overflow-x-auto custom-scrollbar">
                        <table class="w-full text-left text-xs table-fixed min-w-[850px]">
                            <thead><tr class="bg-slate-50 border-b border-slate-200 text-slate-500 font-bold uppercase"><th class="px-4 py-3 w-24 text-center">部門</th><th class="px-4 py-3">機具名稱</th><th class="px-4 py-3 w-48">保養時數 / 備註</th><th class="px-4 py-3 w-32">警示狀態</th><th class="px-4 py-3 w-36 text-center">現場時數登錄</th><th class="px-4 py-3 w-14 text-center">刪除</th></tr></thead>
                            <tbody id="machineTableBody" class="divide-y divide-slate-100 font-semibold text-slate-600"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <div id="content-settings" class="space-y-6 hidden">
                <div class="relative">
                    <div id="settingsLockOverlay" class="absolute inset-0 bg-white/80 backdrop-blur-[1px] rounded-2xl flex flex-col items-center justify-center z-10 transition-all">
                        <div class="bg-slate-800 text-white text-xs px-4 py-2.5 rounded-xl font-bold flex items-center gap-2 shadow-md cursor-pointer hover:bg-slate-900 animate-pulse" onclick="triggerAdminModal()"><svg class="w-4 h-4 text-amber-400" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg>請先解鎖頂部「管理權限」以編輯基礎清冊資料</div>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                            <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2"><svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 2 7 12 12 22 7 12 2" /><polyline points="2 17 12 22 22 17" /><polyline points="2 12 12 17 22 12" /></svg>管理部門選單分類</h3>
                            <div class="flex gap-2 mb-4"><input id="newDeptName" type="text" placeholder="新增部門分類" class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none" /><button onclick="addDepartment()" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 rounded-xl text-xs">新增</button></div>
                            <div id="settingsDeptList" class="space-y-1.5 max-h-56 overflow-y-auto pr-1"></div>
                        </div>
                        <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                            <h3 class="text-sm font-black text-slate-800 mb-4 flex items-center gap-2 border-b border-slate-100 pb-2"><svg class="w-4 h-4 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2" /><circle cx="9" cy="7" r="4" /><path d="M23 21v-2a4 4 0 0 0-3-3.87" /><path d="M16 3.13a4 4 0 0 1 0 7.75" /></svg>管理操作同仁名冊</h3>
                            <div class="flex gap-2 mb-4"><input id="newEmployeeName" type="text" placeholder="新增同仁姓名" class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none" /><button onclick="addEmployee()" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 rounded-xl text-xs">新增</button></div>
                            <div id="settingsEmpList" class="space-y-1.5 max-h-56 overflow-y-auto pr-1"></div>
                        </div>
                    </div>
                </div>

                <!-- LINE 通知設定 -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200/60 shadow-sm">
                    <h3 class="text-sm font-black text-slate-800 mb-1 flex items-center gap-2">
                        <svg class="w-4 h-4" viewBox="0 0 24 24" fill="#06C755"><path d="M12 2C6.48 2 2 5.69 2 10.23c0 4.06 3.55 7.46 8.35 8.1.32.07.77.21.88.49.1.25.07.65.03.9l-.14.85c-.04.25-.2.98.86.53s5.7-3.36 7.78-5.75C21.35 13.66 22 12.02 22 10.23 22 5.69 17.52 2 12 2Z"/></svg>
                        LINE 到期／保養提醒 <span id="lineStatusBadge" class="text-[9px] font-black px-1.5 py-0.5 rounded-md"></span>
                    </h3>
                    <p class="text-[11px] text-slate-400 font-medium mb-4">綁定後，系統只會把「該同仁負責」的到期與機具保養項目，主動私訊給他本人。</p>
                    <div id="lineAdminBox" class="hidden space-y-4">
                        <div>
                            <label class="block text-[11px] font-bold text-slate-500 mb-1.5">LINE 官方帳號存取權杖 (Channel access token)</label>
                            <div class="flex gap-2">
                                <input id="lineTokenInput" type="password" placeholder="貼上 LINE Developers 的長權杖" class="flex-1 bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 focus:outline-none font-mono" />
                                <button onclick="saveLineToken()" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 rounded-xl text-xs whitespace-nowrap">儲存權杖</button>
                            </div>
                        </div>
                        <div>
                            <div class="flex items-center justify-between mb-2"><span class="text-[11px] font-bold text-slate-500">同仁綁定狀態</span><span class="text-[10px] text-slate-400">同仁加官方帳號好友後，傳自己姓名即完成綁定</span></div>
                            <div id="lineBindingList" class="space-y-1.5 max-h-48 overflow-y-auto pr-1 custom-scrollbar"></div>
                        </div>
                        <div class="flex flex-wrap gap-2 pt-1">
                            <button onclick="testLineNotify()" class="text-white font-extrabold px-4 py-2 rounded-xl text-xs shadow-sm" style="background:#06C755">🔔 立即測試發送</button>
                            <button onclick="enableDailyNotify()" class="bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold px-4 py-2 rounded-xl text-xs shadow-sm">啟用每日 08:00 自動提醒</button>
                            <button onclick="disableDailyNotify()" class="bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold px-4 py-2 rounded-xl text-xs">停用自動提醒</button>
                        </div>
                    </div>
                    <div id="lineLockedMsg" class="p-3 bg-slate-50 text-slate-500 rounded-xl text-xs font-bold text-center border border-slate-200 cursor-pointer hover:bg-slate-100" onclick="triggerAdminModal()">🔒 請先解鎖頂部「管理權限」以設定 LINE 通知</div>
                </div>
            </div>
        </main>
    </div>
    </div><!-- /#appRoot -->

    <!-- MODALS -->
    <div id="itemDetailModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl animate-scaleIn">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4"><h3 id="detailModalTitle" class="font-extrabold text-slate-800 text-sm flex items-center gap-1.5"></h3><button onclick="closeDetailModal()" class="text-slate-400 hover:text-slate-700 font-bold text-sm">✕</button></div>
            <div id="detailModalContent" class="space-y-3.5 text-xs text-slate-600 custom-scrollbar max-h-96 overflow-y-auto pr-1"></div>
            <div class="mt-6 pt-3 border-t border-slate-100 flex justify-end"><button onclick="closeDetailModal()" class="bg-slate-800 hover:bg-slate-950 text-white font-extrabold px-5 py-2 rounded-xl text-xs transition shadow-sm active:scale-95">關閉視窗</button></div>
        </div>
    </div>

    <div id="adminModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-sm shadow-2xl">
            <div class="flex items-center gap-2 text-indigo-600 mb-3 border-b border-slate-100 pb-3"><svg class="w-5 h-5 text-indigo-500" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg><h3 class="font-extrabold text-base text-slate-800">解鎖管理權限</h3></div>
            <div class="space-y-4">
                <p class="text-xs text-slate-400 leading-relaxed font-medium">請輸入系統管理密碼，以進行「刪除與基礎清冊異動」等管制操作。</p>
                <input id="adminPasswordInput" type="password" placeholder="請輸入解鎖密碼" class="w-full bg-slate-50 border border-slate-300 rounded-2xl px-4 py-3 text-center text-sm font-bold tracking-widest text-slate-800 focus:ring-2 focus:ring-indigo-400 focus:bg-white focus:outline-none transition" onkeydown="onAdminPasswordKeydown(event)" />
                <div class="flex gap-2"><button type="button" onclick="closeAdminModal()" class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition">取消</button><button type="button" onclick="verifyAdminPassword()" class="flex-1 py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold rounded-xl text-xs transition shadow-sm">送出驗證</button></div>
            </div>
        </div>
    </div>

    <div id="hoursLogModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl">
            <div class="flex items-center gap-2 text-amber-500 mb-3 border-b border-slate-100 pb-3"><svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="12" cy="12" r="10" /><polyline points="12 6 12 12 16 14" /></svg><h3 class="font-extrabold text-base text-slate-800">現場同仁/師傅快速登錄目前時數</h3></div>
            <form onsubmit="submitHoursLog(event)" class="space-y-4">
                <div><label class="block text-xs font-bold text-slate-500 mb-1.5">1. 請選擇欲登錄時數的機具 *</label><select id="logMachineSelect" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2.5 text-xs text-slate-800 font-bold focus:outline-none"></select></div>
                <div><label class="block text-xs font-bold text-slate-500 mb-1.5">2. 請輸入最新的累計總時數 (h) *</label><input id="logHoursInput" type="number" min="0" placeholder="請輸入目前累計的整數時數" class="w-full bg-slate-50 border border-slate-300 rounded-2xl px-4 py-3 text-xl text-center font-bold font-mono text-amber-600 focus:ring-2 focus:ring-amber-400 focus:bg-white focus:outline-none transition" required /></div>
                <div><label class="block text-xs font-bold text-slate-500 mb-1.5">3. 記錄回報人 *</label><select id="logOperatorSelect" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-800 font-bold focus:outline-none"></select></div>
                <div class="flex gap-2 pt-2"><button type="button" onclick="closeHoursLogModal()" class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition">取消</button><button type="submit" class="flex-1 py-2.5 bg-amber-500 hover:bg-amber-600 text-white font-extrabold rounded-xl text-xs transition shadow-sm">確定登錄時數</button></div>
            </form>
        </div>
    </div>

    <div id="maintCompleteModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white border border-slate-200/60 p-6 rounded-3xl w-full max-w-md shadow-2xl">
            <div class="flex items-center gap-2 text-emerald-600 mb-3 border-b border-slate-100 pb-3"><svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12" /></svg><h3 class="font-extrabold text-base text-slate-800">機具定期保養結案</h3></div>
            <form onsubmit="submitMaintComplete(event)" class="space-y-4">
                <input type="hidden" id="maintMachineId" />
                <p class="text-xs text-slate-400 leading-relaxed font-medium">確定已為此機具完成所有定期維護與更換。請指定這台機具的 <span class="text-emerald-600 font-black">「下次保養目標時數」</span>，以重新開啟定時數保養警報。</p>
                <div><label class="block text-xs font-bold text-slate-500 mb-1.5">下次需進行保養的總累計時數 (h) *</label><input id="maintNextHoursInput" type="number" min="1" class="w-full bg-slate-50 border border-slate-300 rounded-2xl px-4 py-3 text-xl text-center font-bold font-mono text-emerald-600 focus:ring-2 focus:ring-emerald-400 focus:bg-white focus:outline-none transition" required /><p class="text-[10px] text-slate-400 mt-1.5 font-medium">💡 建議：若每 250 小時保養一次，此次目標一般為「當前時數 + 250」。</p></div>
                <div class="flex gap-2 pt-2"><button type="button" onclick="closeMaintCompleteModal()" class="flex-1 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition">取消</button><button type="submit" class="flex-1 py-2.5 bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold rounded-xl text-xs transition shadow-sm">確認結案保養</button></div>
            </form>
        </div>
    </div>

    <div id="confirmDialog" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white rounded-3xl max-w-sm w-full p-6 text-center flex flex-col items-center gap-4 shadow-2xl border border-slate-200/60">
            <div class="p-3 bg-rose-50 text-rose-500 rounded-full border border-rose-100"><svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z" /><line x1="12" y1="9" x2="12" y2="13" /><line x1="12" y1="17" x2="12.01" y2="17" /></svg></div>
            <div><h4 class="font-extrabold text-sm text-slate-800">操作確認通知</h4><p id="confirmMessage" class="text-xs text-slate-500 mt-2 font-medium leading-relaxed">確認執行此操作嗎？</p></div>
            <div class="flex gap-2 w-full pt-1"><button onclick="closeConfirmDialog(false)" class="flex-1 py-2 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition">取消</button><button onclick="closeConfirmDialog(true)" class="flex-1 py-2 bg-rose-500 hover:bg-rose-600 text-white font-bold rounded-xl text-xs transition shadow-sm">確認</button></div>
        </div>
    </div>

    <div id="toast" class="fixed bottom-6 right-6 z-[70] hidden"></div>
    <input type="file" id="importFileInput" accept="application/json,.json" class="hidden" onchange="handleImportFile(event)" />

    <footer class="py-4 bg-white border-t border-slate-200 text-center text-[10px] text-slate-400 font-bold uppercase tracking-wider mt-auto">追蹤管理系統 © 2026 • Google 試算表雙軌控制中樞</footer>

    <script>
        var state = {
            activeTab: localStorage.getItem('pref_activeTab') || 'date-reminders',
            currentUser: localStorage.getItem('pref_currentUser') || '',
            dateFilter: localStorage.getItem('pref_dateFilter') || 'pending',
            isAdmin: false, adminPassword: '',
            departments: [], employees: [], dateReminders: [], machines: [], bindings: [], lineReady: false
        };
        var firstLoaded = false;

        function gs(fn) {
            var args = Array.prototype.slice.call(arguments, 1);
            return new Promise(function (resolve, reject) {
                var runner = google.script.run.withSuccessHandler(resolve).withFailureHandler(reject);
                runner[fn].apply(runner, args);
            });
        }

        function esc(str) { return String(str == null ? '' : str).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;').replace(/'/g,'&#39;'); }
        function getTodayDateString() { var d = new Date(); return d.getFullYear()+'-'+String(d.getMonth()+1).padStart(2,'0')+'-'+String(d.getDate()).padStart(2,'0'); }
        function formatROCDateString(dateStr) { if (!dateStr) return ''; var p = String(dateStr).split('-'); if (p.length === 3) return '民國 '+(parseInt(p[0],10)-1911)+' 年 '+p[1]+' 月 '+p[2]+' 日'; return dateStr; }
        function showToast(message, isSuccess) {
            if (isSuccess === undefined) isSuccess = true;
            var el = document.getElementById('toast');
            var icon = isSuccess ? '<svg class="w-4 h-4 text-emerald-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12"/></svg>' : '<svg class="w-4 h-4 text-rose-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>';
            el.innerHTML = '<div class="px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 border bg-slate-900 '+(isSuccess?'border-emerald-500':'border-rose-500')+' text-white font-bold text-xs">'+icon+'<span>'+esc(message)+'</span></div>';
            el.classList.remove('hidden'); clearTimeout(showToast._t); showToast._t = setTimeout(function(){ el.classList.add('hidden'); }, 3500);
        }
        var confirmCallback = null;
        function triggerConfirm(msg, cb) { confirmCallback = cb; document.getElementById('confirmMessage').textContent = msg; document.getElementById('confirmDialog').classList.remove('hidden'); }
        function closeConfirmDialog(ok) { document.getElementById('confirmDialog').classList.add('hidden'); if (ok && confirmCallback) confirmCallback(); confirmCallback = null; }

        function applyData(data) {
            state.departments = data.departments || [];
            state.employees = data.employees || [];
            state.bindings = data.bindings || [];
            state.lineReady = !!data.lineReady;
            state.dateReminders = (data.dateReminders || []).slice().sort(function(a,b){ return new Date(a.expiryDate) - new Date(b.expiryDate); });
            state.machines = (data.machines || []).slice().sort(function(a,b){ return String(a.machineName).localeCompare(String(b.machineName),'zh-Hant'); });
            if (data.user !== undefined) document.getElementById('loginAccountLabel').textContent = data.user || '';
            if (!state.currentUser || state.employees.indexOf(state.currentUser) === -1) state.currentUser = state.employees[0] || '';
            renderAll();
        }
        function initialLoad() {
            gs('getData').then(function (data) {
                applyData(data); firstLoaded = true;
                document.getElementById('appLoading').classList.add('hidden');
                document.getElementById('appRoot').classList.remove('hidden');
                applyActiveTab(); applyDateFilter(); startPolling();
            }).catch(function (err) {
                document.getElementById('appLoading').innerHTML = '<div class="text-center text-rose-500 text-sm font-bold p-6">載入失敗：'+esc(err && err.message ? err.message : err)+'<br><button onclick="location.reload()" class="mt-3 px-4 py-2 bg-slate-800 text-white rounded-xl">重新整理</button></div>';
            });
        }
        function startPolling() {
            setInterval(function () {
                var a = document.activeElement;
                if (a && /INPUT|SELECT|TEXTAREA/.test(a.tagName)) return;
                if (!document.getElementById('confirmDialog').classList.contains('hidden')) return;
                if (!document.getElementById('hoursLogModal').classList.contains('hidden')) return;
                if (!document.getElementById('maintCompleteModal').classList.contains('hidden')) return;
                if (!document.getElementById('editReminderModal').classList.contains('hidden')) return;
                if (!document.getElementById('editMachineModal').classList.contains('hidden')) return;
                gs('getData').then(applyData).catch(function(){});
            }, 4000);
        }

        function openDateItemDetail(id) {
            var item = state.dateReminders.filter(function(r){return r.id===id;})[0]; if (!item) return;
            document.getElementById('detailModalTitle').innerHTML = '<svg class="w-5 h-5 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg><span>📅 待辦提醒詳細資料</span>';
            document.getElementById('detailModalContent').innerHTML =
                '<div class="grid grid-cols-3 gap-2 border-b border-slate-100 pb-3"><div class="text-slate-400 font-bold">品項名稱：</div><div class="col-span-2 text-slate-800 font-extrabold text-sm">'+esc(item.itemName)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">歸屬部門：</div><div class="col-span-2 text-slate-800 font-bold">'+esc(item.department)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">擔當同仁：</div><div class="col-span-2 text-slate-800 font-bold">'+esc(item.manager)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">西元日期：</div><div class="col-span-2 font-mono text-slate-800 font-bold">'+esc(item.expiryDate)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">民國到期：</div><div class="col-span-2 text-emerald-700 font-extrabold">'+esc(formatROCDateString(item.expiryDate))+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">預警天數：</div><div class="col-span-2 text-slate-800 font-medium">到期前 '+esc(item.remindDays1)+' 天、'+esc(item.remindDays2)+' 天、'+esc(item.remindDays3)+' 天發出警告</div></div>'+
                '<div class="border-t border-slate-100 pt-3"><div class="text-slate-400 font-bold mb-1">備註說明：</div><div class="bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-slate-600 font-medium leading-relaxed">'+(esc(item.remark)||'（無額外備註資料）')+'</div></div>'+
                '<div class="border-t border-slate-100 pt-3"><div class="text-slate-400 font-bold mb-1">結案狀態：</div><div class="font-bold">'+(item.completed?'<span class="text-emerald-600">✓ 已結案 (由同仁 ['+esc(item.completedBy)+'] 結案於 '+esc(item.completedAt)+')</span>':'<span class="text-amber-600">⚠️ 尚未結案 (未勾選)</span>')+'</div></div>';
            document.getElementById('itemDetailModal').classList.remove('hidden');
        }
        function openMachineDetail(id) {
            var m = state.machines.filter(function(x){return x.id===id;})[0]; if (!m) return;
            var s = getMachineStatusDetails(m);
            document.getElementById('detailModalTitle').innerHTML = '<svg class="w-5 h-5 text-emerald-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/></svg><span>🚜 機具保養詳細資料</span>';
            document.getElementById('detailModalContent').innerHTML =
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">機具名稱：</div><div class="col-span-2 text-slate-800 font-extrabold text-sm">'+esc(m.machineName)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">歸屬部門：</div><div class="col-span-2 text-slate-800 font-bold">'+esc(m.department)+'</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">當前累計時數：</div><div class="col-span-2 font-mono text-slate-800 font-black text-sm">'+esc(m.currentHours)+' 小時 (h)</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">下次保養目標：</div><div class="col-span-2 font-mono text-indigo-700 font-black text-sm">'+esc(m.nextMaintenanceHours)+' 小時 (h)</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">保養警報臨界：</div><div class="col-span-2 text-slate-800 font-medium">相差剩餘 '+esc(m.warningThreshold)+' 小時即開始預警</div></div>'+
                '<div class="grid grid-cols-3 gap-2"><div class="text-slate-400 font-bold">目前警示狀態：</div><div class="col-span-2"><span class="px-2 py-0.5 rounded border text-[10px] font-bold '+s.color+'">'+esc(s.text)+'</span></div></div>'+
                '<div class="border-t border-slate-100 pt-3"><div class="text-slate-400 font-bold mb-1">保養規格備註：</div><div class="bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-slate-600 font-medium leading-relaxed">'+(esc(m.remark)||'（無特別保養規格登記）')+'</div></div>'+
                '<div class="border-t border-slate-100 pt-3 text-[11px] text-slate-400 flex justify-between"><span>最後時數更新同仁: '+esc(m.lastUpdatedBy)+'</span><span>更新時間: '+esc(m.lastUpdatedAt)+'</span></div>';
            document.getElementById('itemDetailModal').classList.remove('hidden');
        }
        function closeDetailModal() { document.getElementById('itemDetailModal').classList.add('hidden'); }

        function updateAdminUI() {
            var btn = document.getElementById('adminPermissionBtn');
            var lock = document.getElementById('settingsLockOverlay');
            if (state.isAdmin) {
                btn.className = "flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold bg-indigo-50 text-indigo-700 border border-indigo-200 transition shadow-sm active:scale-95";
                btn.innerHTML = '<svg class="w-3.5 h-3.5 text-indigo-600" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 9.9-1"/></svg><span>🔓 管理權限 (已解鎖)</span>';
                if (lock) lock.classList.add('hidden','pointer-events-none');
            } else {
                btn.className = "flex items-center gap-1.5 px-3.5 py-2.5 rounded-2xl text-xs font-extrabold bg-slate-800 text-white border border-slate-700 hover:bg-slate-900 transition shadow-sm active:scale-95";
                btn.innerHTML = '<svg class="w-3.5 h-3.5 text-amber-400" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg><span>🔒 管理權限</span>';
                if (lock) lock.classList.remove('hidden','pointer-events-none');
            }
        }
        function toggleAdminPermission() {
            if (state.isAdmin) { state.isAdmin = false; state.adminPassword = ''; showToast("管理權限已重新鎖定"); updateAdminUI(); renderAll(); }
            else triggerAdminModal();
        }
        function triggerAdminModal() { document.getElementById('adminPasswordInput').value=''; document.getElementById('adminModal').classList.remove('hidden'); setTimeout(function(){document.getElementById('adminPasswordInput').focus();},150); }
        function closeAdminModal() { document.getElementById('adminModal').classList.add('hidden'); }
        function onAdminPasswordKeydown(e) { if (e.key === 'Enter') { e.preventDefault(); verifyAdminPassword(); } }
        function verifyAdminPassword() {
            var pw = document.getElementById('adminPasswordInput').value;
            gs('verifyAdmin', pw).then(function (ok) {
                if (ok) { state.isAdmin = true; state.adminPassword = pw; closeAdminModal(); showToast("管理權限驗證成功！"); updateAdminUI(); renderAll(); }
                else showToast("密碼錯誤，請重新輸入！", false);
            }).catch(function(err){ showToast('驗證失敗：'+(err.message||err), false); });
        }

        function getExpiryStatusDetails(expiryDate, completed) {
            if (completed) return { text:'已完成', color:'bg-emerald-50 text-emerald-700 border-emerald-100 font-bold' };
            var todayTime = new Date(getTodayDateString()).getTime();
            var diffDays = Math.ceil((new Date(expiryDate).getTime() - todayTime) / 86400000);
            if (diffDays < 0) return { text:'已過期 '+Math.abs(diffDays)+' 天', color:'bg-rose-50 text-rose-700 border-rose-100 animate-pulse font-extrabold' };
            if (diffDays === 0) return { text:'今天到期！', color:'bg-amber-100 text-amber-800 border-amber-300 font-black' };
            return { text: diffDays+' 天後到期', color:'bg-slate-100 text-slate-700 border-slate-200' };
        }
        function getMachineStatusDetails(m) {
            var remaining = Number(m.nextMaintenanceHours) - Number(m.currentHours);
            var warning = Number(m.warningThreshold);
            if (remaining <= 0) return { text:'已逾期保養 ('+Math.abs(remaining)+'h)', color:'text-rose-700 bg-rose-50 border-rose-100 font-extrabold', isCritical:true };
            if (remaining <= warning) return { text:'即將保養 (剩 '+remaining+'h)', color:'text-amber-700 bg-amber-50 border-amber-100 font-bold', isCritical:true };
            return { text:'正常 (剩 '+remaining+'h)', color:'text-emerald-700 bg-emerald-50 border-emerald-100 font-medium', isCritical:false };
        }

        function switchTab(t) { state.activeTab = t; localStorage.setItem('pref_activeTab', t); applyActiveTab(); }
        function applyActiveTab() {
            ['date-reminders','machinery-hours','settings'].forEach(function(id){
                document.getElementById('tab-'+id).className = "flex items-center gap-1.5 px-4 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 text-slate-500 hover:text-slate-800 hover:bg-slate-50";
                document.getElementById('content-'+id).classList.add('hidden');
            });
            document.getElementById('tab-'+state.activeTab).className = "flex items-center gap-1.5 px-4 py-2 rounded-xl text-xs font-extrabold transition-all duration-150 bg-emerald-600 text-white shadow-sm";
            document.getElementById('content-'+state.activeTab).classList.remove('hidden');
        }
        function setDateFilter(f) { state.dateFilter = f; localStorage.setItem('pref_dateFilter', f); applyDateFilter(); renderDateTable(); }
        function applyDateFilter() {
            ['all','pending','completed'].forEach(function(f){ document.getElementById('filter-'+f).className = "px-3 py-1.5 rounded-lg transition-all"; });
            var cls = "px-3 py-1.5 rounded-lg transition-all bg-white text-slate-800 shadow-sm font-extrabold";
            if (state.dateFilter === 'pending') cls = "px-3 py-1.5 rounded-lg transition-all bg-amber-100 text-amber-800 shadow-sm font-extrabold";
            else if (state.dateFilter === 'completed') cls = "px-3 py-1.5 rounded-lg transition-all bg-emerald-100 text-emerald-800 shadow-sm font-extrabold";
            document.getElementById('filter-'+state.dateFilter).className = cls;
        }

        function renderAll() { if (!firstLoaded) return; updateAdminUI(); renderUserDropdowns(); renderLeftAlerts(); renderDateTable(); renderMachineTable(); renderSettingsTab(); renderLineCard(); }

        function fillSelect(el, items) {
            if (!el) return;
            var keep = el.value; el.innerHTML = '';
            items.forEach(function(v){ var o=document.createElement('option'); o.value=v; o.textContent=v; el.appendChild(o); });
            if (keep && items.indexOf(keep) !== -1) el.value = keep;
        }
        function renderUserDropdowns() {
            var userSel = document.getElementById('currentUserSelect'); userSel.innerHTML = '';
            state.employees.forEach(function(emp){ var o=document.createElement('option'); o.value=emp; o.textContent=emp; if(emp===state.currentUser)o.selected=true; userSel.appendChild(o); });
            fillSelect(document.getElementById('r-department'), state.departments);
            fillSelect(document.getElementById('r-manager'), state.employees);
            fillSelect(document.getElementById('m-department'), state.departments);
        }
        function onUserSelectChange(el) { state.currentUser = el.value; localStorage.setItem('pref_currentUser', el.value); showToast('操作人切換為：'+el.value); }

        function emptyAlertHtml(title, sub) { return '<div class="text-center py-10 text-slate-400 text-xs flex flex-col items-center justify-center gap-2"><svg class="w-8 h-8 text-emerald-500/60" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12"/></svg><div><p class="font-bold text-slate-500">'+esc(title)+'</p><p class="text-[10px] text-slate-400">'+esc(sub)+'</p></div></div>'; }
        function renderLeftAlerts() {
            var todayTime = new Date(getTodayDateString()).getTime();
            var actives = state.dateReminders.filter(function(item){
                if (item.completed) return false;
                var diff = Math.ceil((new Date(item.expiryDate).getTime()-todayTime)/86400000);
                return (item.remindDays1 && diff<=Number(item.remindDays1)) || (item.remindDays2 && diff<=Number(item.remindDays2)) || (item.remindDays3 && diff<=Number(item.remindDays3)) || diff<0;
            });
            document.getElementById('dateAlertCount').textContent = actives.length;
            var dc = document.getElementById('dateAlertContainer');
            if (!actives.length) { dc.innerHTML = emptyAlertHtml('目前無待辦事項提醒！','所有日期品項均已結案'); }
            else { dc.innerHTML = '';
                actives.forEach(function(item){
                    var s = getExpiryStatusDetails(item.expiryDate, item.completed);
                    var div = document.createElement('div');
                    div.className = "p-3 bg-slate-50/60 rounded-xl border border-slate-200/50 hover:border-amber-400/60 hover:bg-slate-100/50 cursor-pointer transition flex flex-col gap-1.5";
                    div.setAttribute('onclick', "openDateItemDetail('"+item.id+"')");
                    div.innerHTML = '<div class="flex items-start justify-between gap-2"><span class="text-xs font-bold text-slate-800 line-clamp-2">'+esc(item.itemName)+'</span><button onclick="event.stopPropagation(); toggleDateReminder(\''+item.id+'\')" class="flex-shrink-0 p-1 rounded-lg bg-emerald-50 hover:bg-emerald-500 text-emerald-600 hover:text-white transition shadow-sm" title="標記為已完成"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><polyline points="20 6 9 17 4 12"/></svg></button></div><div class="flex flex-wrap items-center justify-between gap-1 text-[10px]"><span class="px-2 py-0.5 rounded-md border text-[9px] '+s.color+'">'+esc(s.text)+'</span><span class="text-slate-400 font-mono">限期：'+esc(item.expiryDate)+'</span></div>';
                    dc.appendChild(div);
                });
            }
            var mAlerts = state.machines.filter(function(m){ return (Number(m.nextMaintenanceHours)-Number(m.currentHours)) <= Number(m.warningThreshold); });
            document.getElementById('machineAlertCount').textContent = mAlerts.length;
            var mc = document.getElementById('machineAlertContainer');
            if (!mAlerts.length) { mc.innerHTML = emptyAlertHtml('目前無機具保養提醒！','所有設備時數均正常'); }
            else { mc.innerHTML = '';
                mAlerts.forEach(function(m){
                    var remaining = Number(m.nextMaintenanceHours)-Number(m.currentHours);
                    var pct = Math.min(100, Math.max(0, (Number(m.currentHours)/Number(m.nextMaintenanceHours))*100));
                    var overdue = remaining <= 0;
                    var div = document.createElement('div');
                    div.className = "p-3.5 rounded-xl border transition cursor-pointer flex flex-col gap-2 " + (overdue?'bg-rose-50/55 border-rose-200 hover:bg-rose-100/40':'bg-amber-50/45 border-amber-200 hover:bg-amber-100/40');
                    div.setAttribute('onclick', "openMachineDetail('"+m.id+"')");
                    div.innerHTML = '<div class="flex items-start justify-between gap-1"><div><h3 class="text-xs font-bold text-slate-800 flex items-center gap-1.5 flex-wrap"><span class="line-clamp-2">'+esc(m.machineName)+'</span></h3><p class="text-[10px] text-slate-400 mt-1 font-semibold">歸屬部門: '+esc(m.department)+'</p></div><button onclick="event.stopPropagation(); openMaintCompleteModal(\''+m.id+'\')" class="text-[10px] font-black bg-emerald-600 text-white px-2.5 py-1 rounded-lg hover:bg-emerald-700 transition flex-shrink-0 shadow-sm whitespace-nowrap">保養完成</button></div><div><div class="flex justify-between text-[10px] font-mono font-bold text-slate-500 mb-1"><span>目前: '+esc(m.currentHours)+'h</span><span class="'+(overdue?'text-rose-600':'text-amber-700')+'">目標: '+esc(m.nextMaintenanceHours)+'h</span></div><div class="w-full h-1.5 bg-slate-200/80 rounded-full overflow-hidden"><div class="h-full rounded-full '+(overdue?'bg-rose-500':'bg-amber-500')+'" style="width:'+pct+'%"></div></div></div><div class="flex items-center justify-between text-[9px] text-slate-400 font-bold"><span class="'+(overdue?'text-rose-600 font-black':'text-amber-700')+'">'+(overdue?'超時 '+Math.abs(remaining)+' 小時!':'剩餘 '+remaining+' 小時保養')+'</span><span>回報人: '+esc(m.lastUpdatedBy)+'</span></div>';
                    mc.appendChild(div);
                });
            }
        }

        function renderDateTable() {
            var tbody = document.getElementById('dateReminderTableBody');
            var q = document.getElementById('searchQuery').value.toLowerCase().trim();
            var filtered = state.dateReminders.filter(function(item){
                if (state.dateFilter==='pending' && item.completed) return false;
                if (state.dateFilter==='completed' && !item.completed) return false;
                if (q) return String(item.itemName).toLowerCase().indexOf(q)>=0 || String(item.department).toLowerCase().indexOf(q)>=0 || String(item.manager).toLowerCase().indexOf(q)>=0;
                return true;
            });
            if (!filtered.length) { tbody.innerHTML = '<tr><td colspan="7" class="text-center py-12 text-slate-400 font-bold">目前無符合篩選條件的提醒項目</td></tr>'; return; }
            tbody.innerHTML = '';
            filtered.forEach(function(item){
                var s = getExpiryStatusDetails(item.expiryDate, item.completed);
                var tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-all " + (item.completed?'opacity-65 bg-slate-50/30':'');
                var del = state.isAdmin
                    ? '<button onclick="event.stopPropagation(); deleteDateReminder(\''+item.id+'\')" class="p-1.5 hover:bg-rose-50 text-slate-400 hover:text-rose-500 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg></button>'
                    : '<button onclick="event.stopPropagation(); triggerAdminModal()" class="p-1.5 text-slate-300 hover:text-slate-500 rounded-lg transition" title="需要管理權限以刪除"><svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg></button>';
                var click = "onclick=\"openDateItemDetail('"+item.id+"')\"";
                tr.innerHTML =
                    '<td class="px-2 py-4 text-center"><div class="flex items-center justify-center"><button onclick="event.stopPropagation(); toggleDateReminder(\''+item.id+'\')" class="w-6 h-6 rounded-lg border-2 flex items-center justify-center transition-all '+(item.completed?'bg-emerald-600 border-emerald-600 text-white':'border-slate-300 hover:border-slate-400 hover:bg-slate-50')+'">'+(item.completed?'<svg class="w-3.5 h-3.5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><polyline points="20 6 9 17 4 12"/></svg>':'')+'</button></div></td>'+
                    '<td '+click+' class="px-2 py-4 text-center cursor-pointer hover:bg-slate-100/50 transition"><span class="bg-slate-100 text-slate-600 border border-slate-200/60 px-2.5 py-0.5 rounded-md font-bold text-[10px] inline-block whitespace-nowrap">'+esc(item.department)+'</span></td>'+
                    '<td '+click+' class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition"><div class="text-xs font-extrabold text-slate-800 line-clamp-2 '+(item.completed?'line-through text-slate-400':'')+'">'+esc(item.itemName)+'</div>'+(item.remark?'<div class="text-[10px] text-slate-400 font-medium italic mt-0.5 line-clamp-1">備註: '+esc(item.remark)+'</div>':'')+(item.completed?'<div class="text-[9px] text-emerald-600 font-bold mt-1">✓ 已結案</div>':'')+'</td>'+
                    '<td '+click+' class="px-4 py-4 text-slate-700 font-semibold cursor-pointer hover:bg-slate-100/50 transition">'+esc(item.manager)+'</td>'+
                    '<td '+click+' class="px-4 py-4 font-mono font-bold text-slate-700 cursor-pointer hover:bg-slate-100/50 transition">'+esc(item.expiryDate)+'</td>'+
                    '<td '+click+' class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition"><span class="px-2 py-0.5 rounded border text-[10px] '+s.color+' whitespace-nowrap inline-block">'+esc(s.text)+'</span></td>'+
                    '<td class="px-4 py-4 text-center">'+del+'</td>';
                tbody.appendChild(tr);
            });
        }

        function renderMachineTable() {
            var tbody = document.getElementById('machineTableBody');
            if (!state.machines.length) { tbody.innerHTML = '<tr><td colspan="6" class="text-center py-12 text-slate-400 font-bold">目前無時數追蹤機具，請於上方建檔</td></tr>'; return; }
            tbody.innerHTML = '';
            state.machines.forEach(function(m){
                var s = getMachineStatusDetails(m);
                var pct = Math.min(100, Math.max(0, (Number(m.currentHours)/Number(m.nextMaintenanceHours))*100));
                var del = state.isAdmin
                    ? '<button onclick="event.stopPropagation(); deleteMachine(\''+m.id+'\')" class="p-1.5 hover:bg-rose-50 text-slate-400 hover:text-rose-500 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg></button>'
                    : '<button onclick="event.stopPropagation(); triggerAdminModal()" class="p-1.5 text-slate-300 hover:text-slate-500 rounded-lg transition" title="需要管理權限以刪除"><svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg></button>';
                var click = "onclick=\"openMachineDetail('"+m.id+"')\"";
                var tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-all";
                tr.innerHTML =
                    '<td '+click+' class="px-4 py-4 text-center cursor-pointer hover:bg-slate-100/50 transition"><span class="bg-slate-100 text-slate-600 border border-slate-200/60 px-2 py-0.5 rounded-md font-bold text-[10px] inline-block whitespace-nowrap">'+esc(m.department)+'</span></td>'+
                    '<td '+click+' class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition"><div class="font-extrabold text-slate-800 text-xs line-clamp-2">'+esc(m.machineName)+'</div></td>'+
                    '<td '+click+' class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition"><div class="flex items-center justify-between text-[10px] font-mono font-bold text-slate-500 mb-1"><span>當前: <span class="text-slate-800 font-black">'+esc(m.currentHours)+'h</span></span><span>目標: <span class="text-indigo-600 font-black">'+esc(m.nextMaintenanceHours)+'h</span></span></div><div class="w-full h-1.5 bg-slate-200/80 rounded-full overflow-hidden mb-1"><div class="h-full rounded-full '+(s.isCritical?'bg-amber-500':'bg-emerald-500')+'" style="width:'+pct+'%"></div></div>'+(m.remark?'<div class="text-[10px] text-slate-400 font-medium italic line-clamp-1">備註: '+esc(m.remark)+'</div>':'')+'</td>'+
                    '<td '+click+' class="px-4 py-4 cursor-pointer hover:bg-slate-100/50 transition"><span class="px-2 py-0.5 rounded border text-[10px] block text-center '+s.color+' whitespace-nowrap">'+esc(s.text)+'</span></td>'+
                    '<td class="px-4 py-4 text-center"><div class="flex justify-center gap-1.5"><button onclick="openSingleHoursLog(\''+m.id+'\', '+Number(m.currentHours)+')" class="bg-amber-500 hover:bg-amber-600 text-white px-2.5 py-1 rounded-lg text-[10px] font-bold whitespace-nowrap shadow-sm active:scale-95 transition">登錄</button><button onclick="openMaintCompleteModal(\''+m.id+'\')" class="bg-emerald-600 hover:bg-emerald-700 text-white px-2.5 py-1 rounded-lg text-[10px] font-bold whitespace-nowrap shadow-sm active:scale-95 transition">完工</button></div></td>'+
                    '<td class="px-4 py-4 text-center">'+del+'</td>';
                tbody.appendChild(tr);
            });
        }

        function renderSettingsTab() {
            var dc = document.getElementById('settingsDeptList'); dc.innerHTML = '';
            state.departments.forEach(function(dept){
                var div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                var del = state.isAdmin ? '<button onclick="deleteDepartment(\''+esc(dept)+'\')" class="text-rose-500 hover:bg-rose-50 p-1 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg></button>' : '';
                div.innerHTML = '<span class="text-xs font-bold text-slate-700">'+esc(dept)+'</span>'+del;
                dc.appendChild(div);
            });
            var ec = document.getElementById('settingsEmpList'); ec.innerHTML = '';
            state.employees.forEach(function(emp){
                var div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                var del = state.isAdmin ? '<button onclick="deleteEmployee(\''+esc(emp)+'\')" class="text-rose-500 hover:bg-rose-50 p-1 rounded-lg transition"><svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg></button>' : '';
                div.innerHTML = '<span class="text-xs font-bold text-slate-700 flex items-center gap-2"><span class="w-2 h-2 rounded-full bg-slate-400"></span>'+esc(emp)+'</span>'+del;
                ec.appendChild(div);
            });
        }

        function renderLineCard() {
            var badge = document.getElementById('lineStatusBadge');
            if (state.lineReady) { badge.textContent = '已啟用'; badge.className = 'text-[9px] font-black px-1.5 py-0.5 rounded-md text-emerald-700 bg-emerald-50 border border-emerald-100'; }
            else { badge.textContent = '未設定'; badge.className = 'text-[9px] font-black px-1.5 py-0.5 rounded-md text-slate-500 bg-slate-100 border border-slate-200'; }
            document.getElementById('lineAdminBox').classList.toggle('hidden', !state.isAdmin);
            document.getElementById('lineLockedMsg').classList.toggle('hidden', state.isAdmin);
            if (!state.isAdmin) return;
            var boundMap = {}; state.bindings.forEach(function(b){ boundMap[b.name] = b; });
            var list = document.getElementById('lineBindingList'); list.innerHTML = '';
            state.employees.forEach(function(name){
                var b = boundMap[name];
                var div = document.createElement('div');
                div.className = "flex items-center justify-between bg-slate-50 p-2.5 rounded-xl border border-slate-200/50";
                var status = (b && b.bound)
                    ? '<span class="text-[10px] font-black text-emerald-700 bg-emerald-50 border border-emerald-100 px-2 py-0.5 rounded-md">✅ 已綁定'+(b.displayName?'（'+esc(b.displayName)+'）':'')+'</span>'
                    : '<span class="text-[10px] font-black text-slate-400 bg-white border border-slate-200 px-2 py-0.5 rounded-md">⚠️ 未綁定</span>';
                div.innerHTML = '<span class="text-xs font-bold text-slate-700">'+esc(name)+'</span>'+status;
                list.appendChild(div);
            });
        }

        function addDateReminder(e) {
            e.preventDefault();
            var itemName = document.getElementById('r-itemName').value.trim();
            var expiryDate = document.getElementById('r-expiryDate').value;
            if (!itemName || !expiryDate) { showToast("請確實填寫品項名稱與到期日期！", false); return; }
            var payload = { itemName: itemName, department: document.getElementById('r-department').value, manager: document.getElementById('r-manager').value, expiryDate: expiryDate, remindDays1: document.getElementById('r-remindDays1').value, remindDays2: document.getElementById('r-remindDays2').value, remindDays3: document.getElementById('r-remindDays3').value, remark: document.getElementById('r-remark').value.trim(), operator: state.currentUser };
            gs('addReminder', payload).then(function(data){ document.getElementById('r-itemName').value=''; document.getElementById('r-remark').value=''; document.getElementById('r-expiryDate').value=''; applyData(data); showToast('已建立提醒事項「'+itemName+'」'); }).catch(function(err){ showToast('新增失敗：'+(err.message||err), false); });
        }
        function toggleDateReminder(id) { gs('toggleReminder', id, state.currentUser).then(function(data){ applyData(data); showToast('已更新事項狀態'); }).catch(function(err){ showToast('更新失敗：'+(err.message||err), false); }); }
        function deleteDateReminder(id) {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            var item = state.dateReminders.filter(function(r){return r.id===id;})[0];
            triggerConfirm('確定要永久刪除「'+(item?item.itemName:'')+'」嗎？此操作無法還原。', function(){ gs('deleteReminder', id, state.adminPassword).then(function(data){ applyData(data); showToast('項目已刪除完成'); }).catch(function(err){ showToast('刪除失敗：'+(err.message||err), false); }); });
        }
        function addMachine(e) {
            e.preventDefault();
            var machineName = document.getElementById('m-machineName').value.trim();
            if (!machineName) { showToast("請確實填寫機具名稱！", false); return; }
            var payload = { machineName: machineName, department: document.getElementById('m-department').value, currentHours: document.getElementById('m-currentHours').value, nextMaintenanceHours: document.getElementById('m-nextMaintenanceHours').value, warningThreshold: document.getElementById('m-warningThreshold').value, remark: document.getElementById('m-remark').value.trim(), operator: state.currentUser };
            gs('addMachine', payload).then(function(data){ document.getElementById('m-machineName').value=''; document.getElementById('m-currentHours').value='0'; document.getElementById('m-nextMaintenanceHours').value='250'; document.getElementById('m-warningThreshold').value='50'; document.getElementById('m-remark').value=''; applyData(data); showToast('機具「'+machineName+'」已登錄成功！'); }).catch(function(err){ showToast('新增失敗：'+(err.message||err), false); });
        }
        function deleteMachine(id) {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            var m = state.machines.filter(function(x){return x.id===id;})[0];
            triggerConfirm('確定要刪除「'+(m?m.machineName:'')+'」嗎？所有保養數據將被清除。', function(){ gs('deleteMachine', id, state.adminPassword).then(function(data){ applyData(data); showToast('機具檔案已永久刪除'); }).catch(function(err){ showToast('刪除失敗：'+(err.message||err), false); }); });
        }

        function openHoursLogModal() {
            if (!state.machines.length) { showToast("目前尚無機具檔案，請先新增機具！", false); return; }
            var sel = document.getElementById('logMachineSelect'); sel.innerHTML = '';
            state.machines.forEach(function(m){ var o=document.createElement('option'); o.value=m.id; o.textContent=m.machineName+' (目前: '+m.currentHours+'h)'; sel.appendChild(o); });
            document.getElementById('logHoursInput').value = state.machines[0].currentHours;
            var op = document.getElementById('logOperatorSelect'); op.innerHTML = '';
            state.employees.forEach(function(emp){ var o=document.createElement('option'); o.value=emp; o.textContent=emp; if(emp===state.currentUser)o.selected=true; op.appendChild(o); });
            document.getElementById('hoursLogModal').classList.remove('hidden');
        }
        function openSingleHoursLog(id, cur) { openHoursLogModal(); document.getElementById('logMachineSelect').value = id; document.getElementById('logHoursInput').value = cur; }
        function closeHoursLogModal() { document.getElementById('hoursLogModal').classList.add('hidden'); }
        function submitHoursLog(e) {
            e.preventDefault();
            var id = document.getElementById('logMachineSelect').value;
            var hours = Number(document.getElementById('logHoursInput').value);
            var op = document.getElementById('logOperatorSelect').value;
            if (isNaN(hours) || hours < 0) { showToast("請輸入合理的總累計時數！", false); return; }
            gs('logHours', id, hours, op).then(function(data){ closeHoursLogModal(); applyData(data); showToast('目前時數更新完成！'); }).catch(function(err){ showToast('更新失敗：'+(err.message||err), false); });
        }
        function openMaintCompleteModal(id) {
            var m = state.machines.filter(function(x){return x.id===id;})[0]; if (!m) return;
            document.getElementById('maintMachineId').value = id;
            document.getElementById('maintNextHoursInput').value = Number(m.currentHours) + 250;
            document.getElementById('maintCompleteModal').classList.remove('hidden');
        }
        function closeMaintCompleteModal() { document.getElementById('maintCompleteModal').classList.add('hidden'); }
        function submitMaintComplete(e) {
            e.preventDefault();
            var id = document.getElementById('maintMachineId').value;
            var next = Number(document.getElementById('maintNextHoursInput').value);
            if (isNaN(next) || next <= 0) { showToast("保養目標時數輸入不合理！", false); return; }
            gs('completeMaint', id, next, state.currentUser).then(function(data){ closeMaintCompleteModal(); applyData(data); showToast('機具保養完工結案確認！'); }).catch(function(err){ showToast('更新失敗：'+(err.message||err), false); });
        }

        function addDepartment() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            var name = document.getElementById('newDeptName').value.trim(); if (!name) return;
            if (state.departments.indexOf(name)>=0) { showToast("此部門名稱已存在！", false); return; }
            gs('addDepartment', name, state.adminPassword).then(function(data){ document.getElementById('newDeptName').value=''; applyData(data); showToast('已新增部門分類「'+name+'」'); }).catch(function(err){ showToast('新增失敗：'+(err.message||err), false); });
        }
        function deleteDepartment(name) {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            if (state.departments.length <= 1) { showToast("必須保留至少一個部門分類！", false); return; }
            triggerConfirm('確定要刪除部門分類「'+name+'」嗎？', function(){ gs('deleteDepartment', name, state.adminPassword).then(function(data){ applyData(data); showToast('部門分類已移除'); }).catch(function(err){ showToast('刪除失敗：'+(err.message||err), false); }); });
        }
        function addEmployee() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            var name = document.getElementById('newEmployeeName').value.trim(); if (!name) return;
            if (state.employees.indexOf(name)>=0) { showToast("此同仁姓名已存在！", false); return; }
            gs('addEmployee', name, state.adminPassword).then(function(data){ document.getElementById('newEmployeeName').value=''; applyData(data); showToast('同仁「'+name+'」已成功加入'); }).catch(function(err){ showToast('新增失敗：'+(err.message||err), false); });
        }
        function deleteEmployee(name) {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            if (state.employees.length <= 1) { showToast("名冊中必須保留至少一位同仁！", false); return; }
            triggerConfirm('確定要將「'+name+'」從同仁名冊中移除嗎？', function(){ gs('deleteEmployee', name, state.adminPassword).then(function(data){ if(state.currentUser===name){state.currentUser=data.employees[0]||''; localStorage.setItem('pref_currentUser',state.currentUser);} applyData(data); showToast('同仁已自名冊移除'); }).catch(function(err){ showToast('刪除失敗：'+(err.message||err), false); }); });
        }

        // ---- LINE ----
        function saveLineToken() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            var t = document.getElementById('lineTokenInput').value.trim();
            if (!t) { showToast('請先貼上權杖', false); return; }
            gs('setLineToken', state.adminPassword, t).then(function(data){ document.getElementById('lineTokenInput').value=''; applyData(data); showToast('LINE 權杖已儲存，通知已啟用'); }).catch(function(err){ showToast('儲存失敗：'+(err.message||err), false); });
        }
        function testLineNotify() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            if (!state.lineReady) { showToast('請先儲存 LINE 權杖', false); return; }
            showToast('發送中…');
            gs('sendTestNotify', state.adminPassword).then(function(res){
                if (!res.ok) { showToast(res.msg||'尚未設定', false); return; }
                var msg = '已私訊 '+res.sent+' 人';
                if (res.skipped && res.skipped.length) msg += '；未綁定略過：'+res.skipped.join('、');
                if (res.sent===0 && (!res.skipped||!res.skipped.length)) msg = '目前沒有需要提醒的項目';
                showToast(msg);
            }).catch(function(err){ showToast('發送失敗：'+(err.message||err), false); });
        }
        function enableDailyNotify() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            if (!state.lineReady) { showToast('請先儲存 LINE 權杖', false); return; }
            gs('enableDailyNotify', state.adminPassword, 8).then(function(){ showToast('已啟用每日 08:00 自動提醒'); }).catch(function(err){ showToast('啟用失敗：'+(err.message||err), false); });
        }
        function disableDailyNotify() {
            if (!state.isAdmin) { triggerAdminModal(); return; }
            gs('disableDailyNotify', state.adminPassword).then(function(){ showToast('已停用每日自動提醒'); }).catch(function(err){ showToast('停用失敗：'+(err.message||err), false); });
        }

        function exportBackup() {
            var data = { exportedAt:new Date().toString(), departments:state.departments, employees:state.employees, dateReminders:state.dateReminders, machines:state.machines };
            var blob = new Blob([JSON.stringify(data,null,2)], {type:'application/json'});
            var url = URL.createObjectURL(blob);
            var a = document.createElement('a'); var d = new Date();
            a.href = url; a.download = '追蹤系統備份_'+d.getFullYear()+String(d.getMonth()+1).padStart(2,'0')+String(d.getDate()).padStart(2,'0')+'.json';
            a.click(); URL.revokeObjectURL(url); showToast('已匯出 JSON 備份');
        }
        function handleImportFile(e) {
            var file = e.target.files[0]; if (!file) return;
            if (!state.isAdmin) { showToast("匯入需先解鎖管理權限", false); e.target.value=''; return; }
            var reader = new FileReader();
            reader.onload = function() {
                try {
                    var data = JSON.parse(reader.result);
                    triggerConfirm('匯入會把備份中的品項與機具「新增」到試算表（不刪除現有資料），確定嗎？', function(){ gs('importData', data, state.adminPassword).then(function(res){ applyData(res.data); showToast('已匯入 '+res.count+' 筆資料'); }).catch(function(err){ showToast('匯入失敗：'+(err.message||err), false); }); });
                } catch(err) { showToast('匯入失敗：檔案格式錯誤', false); }
                e.target.value = '';
            };
            reader.readAsText(file);
        }

        window.onload = initialLoad;
    </script>
</body>
</html>
