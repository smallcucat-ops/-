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
