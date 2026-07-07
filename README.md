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
