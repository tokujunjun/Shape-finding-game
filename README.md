# Shape-finding-game
<!DOCTYPE html>
<html lang="ja" class="h-full bg-slate-50">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>形探しトレーニング (Shape Search Game)</title>
  
  <!-- OGP & Social Sharing Tags for GitHub Pages -->
  <meta property="og:title" content="形探しトレーニング (Shape Search Game)">
  <meta property="og:description" content="お子様や児童向けのビジョントレーニング・形探しゲーム＆印刷用プリント作成ツール">
  <meta property="og:type" content="website">
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['"M PLUS Rounded 1c"', 'sans-serif'],
          },
          colors: {
            brand: {
              50: '#f0f9ff',
              100: '#e0f2fe',
              500: '#0ea5e9',
              600: '#0284c7',
              700: '#0369a1',
            }
          }
        }
      }
    }
  </script>
  
  <!-- Google Fonts: M PLUS Rounded 1c for friendly readable UI -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@400;700;800;900&display=swap" rel="stylesheet">
  
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

  <style>
    /* Custom Animations & Touch Utilities */
    @keyframes popIn {
      0% { transform: scale(0.6); opacity: 0; }
      70% { transform: scale(1.1); }
      100% { transform: scale(1); opacity: 1; }
    }
    @keyframes shake {
      0%, 100% { transform: translateX(0); }
      20%, 60% { transform: translateX(-6px); }
      40%, 80% { transform: translateX(6px); }
    }
    .animate-pop { animation: popIn 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }
    .animate-shake { animation: shake 0.3s ease-in-out; }
    
    /* Print Styles for Printable Worksheet */
    @media print {
      body { background: white !important; color: black !important; }
      .no-print { display: none !important; }
      .print-only { display: block !important; }
      .worksheet-page {
        width: 100% !important;
        max-width: 100% !important;
        margin: 0 !important;
        padding: 0 !important;
        box-shadow: none !important;
        border: none !important;
      }
    }
    .print-only { display: none; }
  </style>
</head>
<body class="h-screen overflow-hidden font-sans text-slate-800 antialiased flex flex-col justify-between select-none bg-slate-50">

  <!-- Top Navigation Header -->
  <header class="bg-white border-b border-slate-200 shrink-0 z-30 no-print shadow-sm">
    <div class="max-w-4xl mx-auto px-3 sm:px-4 h-16 flex items-center justify-between">
      <div class="flex items-center space-x-2">
        <div class="bg-amber-400 text-white w-9 h-9 sm:w-10 sm:h-10 rounded-xl flex items-center justify-center font-extrabold text-lg sm:text-xl shadow-sm transform -rotate-3">
          <i class="fa-solid fa-shapes"></i>
        </div>
        <div>
          <h1 class="text-base sm:text-xl font-black text-slate-800 tracking-tight leading-tight">形探し</h1>
        </div>
      </div>

      <!-- Navigation Tabs (Slightly Larger Buttons) -->
      <nav class="flex bg-slate-100 p-1.5 rounded-2xl text-xs sm:text-sm font-black space-x-1 border border-slate-200/60">
        <button id="nav-app-btn" onclick="switchTab('app')" class="px-3 sm:px-4 py-2 rounded-xl text-brand-600 bg-white shadow-xs transition-all flex items-center space-x-1.5">
          <i class="fa-solid fa-gamepad text-base sm:text-lg"></i>
          <span>ゲーム</span>
        </button>
        <button id="nav-records-btn" onclick="switchTab('records')" class="px-3 sm:px-4 py-2 rounded-xl text-slate-600 hover:text-slate-900 transition-all flex items-center space-x-1.5">
          <i class="fa-solid fa-trophy text-amber-500 text-base sm:text-lg"></i>
          <span>きろく</span>
        </button>
        <button id="nav-print-btn" onclick="switchTab('print')" class="px-3 sm:px-4 py-2 rounded-xl text-slate-600 hover:text-slate-900 transition-all flex items-center space-x-1.5">
          <i class="fa-solid fa-print text-base sm:text-lg"></i>
          <span>プリント作成</span>
        </button>
      </nav>
    </div>
  </header>

  <!-- Main Content Body -->
  <main class="grow max-w-4xl w-full mx-auto p-2 sm:p-4 flex flex-col justify-center overflow-y-auto">

    <!-- ========================================== -->
    <!-- TAB 1: INTERACTIVE GAME APP                -->
    <!-- ========================================== -->
    <div id="tab-app" class="space-y-3">
      
      <!-- GAME SETUP CARD (Displayed before game start) -->
      <div id="game-setup-card" class="bg-white rounded-2xl p-4 sm:p-6 shadow-md border border-slate-100 max-w-md mx-auto transform transition-all my-auto">
        <div class="text-center mb-4">
          <span class="bg-amber-100 text-amber-800 font-extrabold text-xs px-2.5 py-0.5 rounded-full uppercase tracking-wider">設定メニュー</span>
          <h2 class="text-xl font-black text-slate-800 mt-1">ゲームの条件をえらぼう</h2>
        </div>

        <form id="setup-form" onsubmit="event.preventDefault(); startGame();" class="space-y-3.5">
          
          <!-- 1. Target Shape Count (1 or 2 shapes) -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">
              <i class="fa-solid fa-crosshairs text-brand-500 mr-1"></i> さがす形の種類
            </label>
            <div class="grid grid-cols-2 gap-2">
              <label class="cursor-pointer">
                <input type="radio" name="targetCount" value="1" checked class="peer sr-only" onchange="updateSetupUI()">
                <div class="p-2 text-center rounded-xl border-2 border-slate-200 peer-checked:border-brand-500 peer-checked:bg-brand-50 font-bold text-xs text-slate-700 peer-checked:text-brand-700 transition-all flex items-center justify-center space-x-1.5">
                  <span class="text-base">★</span>
                  <span>1種類</span>
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="targetCount" value="2" class="peer sr-only" onchange="updateSetupUI()">
                <div class="p-2 text-center rounded-xl border-2 border-slate-200 peer-checked:border-brand-500 peer-checked:bg-brand-50 font-bold text-xs text-slate-700 peer-checked:text-brand-700 transition-all flex items-center justify-center space-x-1.5">
                  <span class="text-base">★ ●</span>
                  <span>2種類</span>
                </div>
              </label>
            </div>
          </div>

          <!-- 2. Game Mode Selection -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">
              <i class="fa-solid fa-stopwatch text-indigo-500 mr-1"></i> チャレンジモード
            </label>
            <div class="grid grid-cols-2 gap-2">
              <label class="cursor-pointer">
                <input type="radio" name="gameMode" value="time-limit" checked class="peer sr-only" onchange="toggleModeSettings()">
                <div class="p-2 text-center rounded-xl border-2 border-slate-200 peer-checked:border-indigo-500 peer-checked:bg-indigo-50 font-bold text-xs text-slate-700 peer-checked:text-indigo-700 transition-all">
                  <i class="fa-solid fa-hourglass-half mb-0.5 text-sm block"></i>
                  <span>制限時間モード</span>
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="gameMode" value="speed-run" class="peer sr-only" onchange="toggleModeSettings()">
                <div class="p-2 text-center rounded-xl border-2 border-slate-200 peer-checked:border-emerald-500 peer-checked:bg-emerald-50 font-bold text-xs text-slate-700 peer-checked:text-emerald-700 transition-all">
                  <i class="fa-solid fa-bolt mb-0.5 text-sm block"></i>
                  <span>タイムアタック</span>
                </div>
              </label>
            </div>
          </div>

          <!-- 3. Dynamic Setting (Time Limit OR Target Goal) -->
          <div id="setting-time-limit" class="bg-slate-50 p-2.5 rounded-xl border border-slate-200">
            <label class="block text-xs font-bold text-slate-700 mb-1.5">制限時間設定</label>
            <div class="grid grid-cols-3 gap-2">
              <label class="cursor-pointer">
                <input type="radio" name="timeVal" value="15" class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-indigo-600 peer-checked:bg-indigo-600 peer-checked:text-white font-black text-xs transition-all">
                  15秒
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="timeVal" value="30" checked class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-indigo-600 peer-checked:bg-indigo-600 peer-checked:text-white font-black text-xs transition-all">
                  30秒
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="timeVal" value="60" class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-indigo-600 peer-checked:bg-indigo-600 peer-checked:text-white font-black text-xs transition-all">
                  60秒
                </div>
              </label>
            </div>
          </div>

          <div id="setting-speed-run" class="bg-slate-50 p-2.5 rounded-xl border border-slate-200 hidden">
            <label class="block text-xs font-bold text-slate-700 mb-1.5">目標見つける個数</label>
            <div class="grid grid-cols-3 gap-2">
              <label class="cursor-pointer">
                <input type="radio" name="goalVal" value="5" class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-emerald-600 peer-checked:bg-emerald-600 peer-checked:text-white font-black text-xs transition-all">
                  5個
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="goalVal" value="10" checked class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-emerald-600 peer-checked:bg-emerald-600 peer-checked:text-white font-black text-xs transition-all">
                  10個
                </div>
              </label>
              <label class="cursor-pointer">
                <input type="radio" name="goalVal" value="20" class="peer sr-only">
                <div class="py-1.5 text-center rounded-lg border border-slate-300 peer-checked:border-emerald-600 peer-checked:bg-emerald-600 peer-checked:text-white font-black text-xs transition-all">
                  20個
                </div>
              </label>
            </div>
          </div>

          <!-- 4. Shape Theme Category -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">
              <i class="fa-solid fa-icons text-pink-500 mr-1"></i> 形の種類テーマ
            </label>
            <select id="shape-category" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-2.5 py-2 font-bold text-xs text-slate-700 focus:outline-none focus:ring-2 focus:ring-brand-500">
              <option value="symbols">きほんの図形（★ ● ▲ ■ ◆ など）</option>
              <option value="animals">たのしい動物（🐱 🐶 🐰 🐼 🦊 など）</option>
              <option value="food">食べもの（🍎 🍌 🍓 🍇 🍊 など）</option>
              <option value="vehicles">乗りもの（🚗 🚀 ✈️ 🚃 🚢 など）</option>
            </select>
          </div>

          <!-- Start Button -->
          <button type="submit" class="w-full bg-emerald-500 hover:bg-emerald-600 active:scale-98 text-white font-black text-lg py-3 rounded-xl shadow-md shadow-emerald-200 transition-all flex items-center justify-center space-x-2">
            <i class="fa-solid fa-play"></i>
            <span>ゲームスタート！</span>
          </button>
        </form>
      </div>

      <!-- GAMEPLAY ARENA (Hidden until game starts) -->
      <div id="game-arena" class="hidden space-y-2.5 my-auto">
        
        <!-- Header Info Bar -->
        <div class="bg-white rounded-2xl p-2.5 shadow-sm border border-slate-100 flex items-center justify-between gap-2 max-w-xl mx-auto">
          
          <!-- Target Shapes Prompt -->
          <div class="flex items-center space-x-2 bg-amber-50 border border-amber-200 px-3 py-1.5 rounded-xl">
            <span class="text-xs font-black text-amber-800 uppercase tracking-wider whitespace-nowrap">さがす形:</span>
            <div id="target-display" class="flex space-x-2 text-2xl font-black text-amber-600"></div>
          </div>

          <!-- Timer & Counter Display -->
          <div class="flex items-center space-x-4">
            <!-- Timer -->
            <div class="text-center">
              <span class="text-[10px] font-bold text-slate-400 block uppercase leading-none">じかん</span>
              <span id="game-timer" class="text-xl sm:text-2xl font-black text-slate-800 font-mono">00:00</span>
            </div>

            <!-- Found Counter -->
            <div class="text-center">
              <span class="text-[10px] font-bold text-slate-400 block uppercase leading-none">みつけた</span>
              <span id="game-counter" class="text-xl sm:text-2xl font-black text-brand-600">0<span id="counter-goal-suffix" class="text-xs font-bold text-slate-400">個</span></span>
            </div>

            <!-- Quit Button -->
            <button onclick="abortGame()" class="bg-slate-100 hover:bg-slate-200 text-slate-600 w-8 h-8 rounded-lg flex items-center justify-center font-bold text-base transition-all" title="やめる">
              <i class="fa-solid fa-xmark"></i>
            </button>
          </div>
        </div>

        <!-- Interactive Grid Board -->
        <div class="bg-white rounded-2xl p-3 shadow-md border border-slate-100 max-w-xl mx-auto">
          <div id="grid-board" class="grid grid-cols-5 sm:grid-cols-6 gap-2">
            <!-- Grid tiles will be injected dynamically via JS -->
          </div>
        </div>

        <!-- Status hint -->
        <div class="text-center text-[11px] font-bold text-slate-400">
          <i class="fa-solid fa-lightbulb text-amber-400 mr-1"></i> 見本と同じ形を見つけたら、どんどんタップしよう！
        </div>

      </div>

    </div>

    <!-- GAME RESULT MODAL -->
    <div id="result-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4 z-50 hidden animate-pop">
      <div class="bg-white rounded-3xl max-w-md w-full p-6 sm:p-8 text-center shadow-2xl space-y-6">
        
        <div class="inline-flex items-center justify-center w-20 h-20 bg-amber-100 text-amber-500 rounded-full text-4xl shadow-inner">
          <i id="result-icon" class="fa-solid fa-trophy"></i>
        </div>

        <div>
          <span class="text-xs font-black text-brand-600 bg-brand-50 px-3 py-1 rounded-full uppercase tracking-widest">ゲームしゅうりょう！</span>
          <h3 id="result-title" class="text-2xl sm:text-3xl font-black text-slate-800 mt-2">すごい！大せいこう！</h3>
        </div>

        <!-- Result Stats Card -->
        <div class="bg-slate-50 border border-slate-200 rounded-2xl p-4 space-y-3">
          <div class="flex justify-between items-center text-slate-600 text-sm font-bold border-b border-slate-200 pb-2">
            <span>プレイ条件:</span>
            <span id="res-mode-label" class="text-slate-900 font-extrabold">1種類 / 30秒制限</span>
          </div>
          <div class="flex justify-between items-center text-slate-600 text-sm font-bold border-b border-slate-200 pb-2">
            <span>見つけた個数:</span>
            <span id="res-found-count" class="text-xl font-black text-brand-600">0 個</span>
          </div>
          <div class="flex justify-between items-center text-slate-600 text-sm font-bold">
            <span>かかった時間 / 残り時間:</span>
            <span id="res-time-value" class="text-lg font-black text-slate-800">0.0 秒</span>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="flex space-x-3">
          <button onclick="closeResultModal(); startGame();" class="flex-1 bg-brand-500 hover:bg-brand-600 text-white font-black py-3.5 rounded-xl shadow-md transition-all">
            <i class="fa-solid fa-rotate-right mr-1"></i> もういちど
          </button>
          <button onclick="closeResultModal(); switchTab('records');" class="bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold px-4 py-3.5 rounded-xl transition-all">
            きろくを見る
          </button>
        </div>

      </div>
    </div>


    <!-- ========================================== -->
    <!-- TAB 2: RECORDS & PERFORMANCE HISTORY       -->
    <!-- ========================================== -->
    <div id="tab-records" class="hidden space-y-6">
      
      <div class="bg-white rounded-3xl p-6 sm:p-8 shadow-xl border border-slate-100 space-y-6">
        <div class="flex flex-wrap items-center justify-between gap-4 border-b border-slate-100 pb-4">
          <div>
            <h2 class="text-2xl font-black text-slate-800">みつけた記録リスト</h2>
            <p class="text-xs font-bold text-slate-400">過去のプレイ成果と記録が確認できます</p>
          </div>
          <button onclick="confirmClearHistory()" class="text-xs font-bold text-rose-500 hover:text-rose-700 bg-rose-50 hover:bg-rose-100 px-3 py-2 rounded-xl transition-all">
            <i class="fa-solid fa-trash-can mr-1"></i> 記録を消去
          </button>
        </div>

        <!-- High Score Badges Overview -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div class="bg-gradient-to-br from-amber-50 to-orange-50 border border-amber-200 rounded-2xl p-4 flex items-center space-x-3">
            <div class="w-12 h-12 bg-amber-400 text-white rounded-xl flex items-center justify-center text-xl font-black shadow-md">
              <i class="fa-solid fa-crown"></i>
            </div>
            <div>
              <span class="text-xs font-bold text-amber-700 block">30秒モード最高</span>
              <span id="best-30s" class="text-xl font-black text-slate-800">-- 個</span>
            </div>
          </div>

          <div class="bg-gradient-to-br from-indigo-50 to-blue-50 border border-indigo-200 rounded-2xl p-4 flex items-center space-x-3">
            <div class="w-12 h-12 bg-indigo-500 text-white rounded-xl flex items-center justify-center text-xl font-black shadow-md">
              <i class="fa-solid fa-stopwatch"></i>
            </div>
            <div>
              <span class="text-xs font-bold text-indigo-700 block">15秒モード最高</span>
              <span id="best-15s" class="text-xl font-black text-slate-800">-- 個</span>
            </div>
          </div>

          <div class="bg-gradient-to-br from-emerald-50 to-teal-50 border border-emerald-200 rounded-2xl p-4 flex items-center space-x-3">
            <div class="w-12 h-12 bg-emerald-500 text-white rounded-xl flex items-center justify-center text-xl font-black shadow-md">
              <i class="fa-solid fa-bolt"></i>
            </div>
            <div>
              <span class="text-xs font-bold text-emerald-700 block">10個タイム最速</span>
              <span id="best-10item" class="text-xl font-black text-slate-800">-- 秒</span>
            </div>
          </div>
        </div>

        <!-- History Table -->
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse">
            <thead>
              <tr class="border-b border-slate-200 text-xs font-black text-slate-400 uppercase tracking-wider">
                <th class="py-3 px-3">日時</th>
                <th class="py-3 px-3">探す形</th>
                <th class="py-3 px-3">モード / 条件</th>
                <th class="py-3 px-3 text-right">結果</th>
              </tr>
            </thead>
            <tbody id="history-table-body" class="divide-y divide-slate-100 font-bold text-sm text-slate-700">
              <!-- History Rows injected via JS -->
            </tbody>
          </table>
          <div id="no-history-msg" class="text-center py-10 text-slate-400 font-bold text-sm hidden">
            まだプレイ記録がありません。ゲームをプレイしてみよう！
          </div>
        </div>

      </div>

    </div>

    <!-- ========================================== -->
    <!-- TAB 3: PRINTABLE WORKSHEET GENERATOR       -->
    <!-- ========================================== -->
    <div id="tab-print" class="hidden space-y-6">
      
      <!-- Printable Control Panel (Hidden on Print) -->
      <div class="bg-white rounded-3xl p-6 shadow-xl border border-slate-100 no-print space-y-4">
        <div class="flex flex-wrap items-center justify-between gap-4 border-b border-slate-100 pb-3">
          <div>
            <h2 class="text-xl font-black text-slate-800">印刷用「形探しワークシート」作成</h2>
            <p class="text-xs font-bold text-slate-400">紙と鉛筆でチャレンジできるワークシートを生成します（1種類 / 2種類対応）</p>
          </div>
          
          <button onclick="window.print()" class="bg-brand-500 hover:bg-brand-600 text-white font-black px-5 py-2.5 rounded-xl shadow-md transition-all flex items-center space-x-2">
            <i class="fa-solid fa-print"></i>
            <span>印刷する (Print)</span>
          </button>
        </div>

        <!-- Worksheet Generation Controls -->
        <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 text-sm font-bold">
          <div>
            <label class="block text-slate-600 mb-1 text-xs">探す形の種類</label>
            <select id="ws-target-count" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-200 rounded-xl p-2 font-bold text-xs sm:text-sm">
              <option value="1">1種類</option>
              <option value="2">2種類</option>
            </select>
          </div>

          <div>
            <label class="block text-slate-600 mb-1 text-xs">形の種類テーマ</label>
            <select id="ws-category" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-200 rounded-xl p-2 font-bold text-xs sm:text-sm">
              <option value="symbols">きほんの図形</option>
              <option value="animals">たのしい動物</option>
              <option value="food">食べもの</option>
              <option value="vehicles">乗りもの</option>
            </select>
          </div>

          <div>
            <label class="block text-slate-600 mb-1 text-xs">マス目の大きさ</label>
            <select id="ws-grid-size" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-200 rounded-xl p-2 font-bold text-xs sm:text-sm">
              <option value="30">かんたん (30マス)</option>
              <option value="42" selected>ふつう (42マス)</option>
              <option value="56">むずかしい (56マス)</option>
            </select>
          </div>

          <div>
            <label class="block text-slate-600 mb-1 text-xs">再生成</label>
            <button onclick="generateWorksheet()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-700 py-2 rounded-xl transition-all font-extrabold text-xs sm:text-sm">
              <i class="fa-solid fa-dice mr-1"></i> 新しくつくる
            </button>
          </div>
        </div>
      </div>

      <!-- Printable Sheet Preview Area (A4 Friendly Layout) -->
      <div id="worksheet-paper" class="worksheet-page bg-white p-8 rounded-3xl shadow-lg border border-slate-200 max-w-2xl mx-auto space-y-6 text-slate-900">
        
        <!-- Sheet Header -->
        <div class="border-b-2 border-slate-800 pb-4 flex justify-between items-end">
          <div>
            <h2 class="text-2xl font-black tracking-tight">かたち探し ワークシート</h2>
            <p class="text-xs font-bold text-slate-600 mt-1">見本（みほん）と同じ形を見つけて○をつけよう！</p>
          </div>
          <!-- Left-aligned name/date box with ample room for writing -->
          <div class="text-left text-xs font-bold border-2 border-slate-800 rounded-xl px-3 py-2 space-y-1.5 w-48">
            <div class="text-slate-700">　月　　日（　）</div>
            <div class="pt-1 border-t border-slate-300 text-slate-800 font-black">なまえ：</div>
          </div>
        </div>

        <!-- Target Box -->
        <div class="bg-slate-50 border-2 border-dashed border-slate-400 rounded-2xl p-4 text-center">
          <span class="text-xs font-black text-slate-600 block mb-2">【 さがす かたち 】</span>
          <div id="ws-target-box" class="flex justify-center items-center text-3xl sm:text-4xl font-black text-slate-900"></div>
        </div>

        <!-- Grid Items -->
        <div id="ws-grid-board" class="grid gap-3 py-4 border-2 border-slate-800 rounded-2xl p-4 bg-white">
          <!-- Printable items injected JS -->
        </div>

        <!-- Printable Record Footer Box -->
        <div class="border-2 border-slate-800 rounded-2xl p-4 space-y-2 text-xs font-bold">
          <div class="font-black text-sm border-b border-slate-300 pb-1">きろくコーナー</div>
          <div class="grid grid-cols-2 gap-4 pt-1">
            <div class="space-y-1">
              <div>■ 制限時間（せいげんじかん）モード</div>
              <div class="pl-2 text-slate-600">（ [ ] 15秒  [ ] 30秒  [ ] 60秒 ）</div>
              <div class="pt-1">みつけた個数： <b>【　　　　　】 個</b></div>
            </div>
            <div class="space-y-1">
              <div>■ かかった時間（じかん）計測</div>
              <div class="pl-2 text-slate-600">ぜんぶ見つけるまでにかかった時間</div>
              <div class="pt-1">タイム： <b>【　　　分　　　秒】</b></div>
            </div>
          </div>
        </div>

      </div>

    </div>

  </main>

  <!-- JavaScript Code Base -->
  <script>
    // --- GLOBAL SHAPE DEFINITIONS & GAME STATE ---
    const SHAPE_SETS = {
      symbols: ['★', '●', '▲', '■', '◆', '♥', '♠', '✦', '⬟', '⬢', '✪', '✖'],
      animals: ['🐱', '🐶', '🐰', '🐼', '🦊', '🐻', '🐸', '🦁', '🐵', '🐷', '🐥', '🐯'],
      food: ['🍎', '🍌', '🍓', '🍇', '🍊', '🍉', '🍕', '🍩', '🍦', '🍰', '🍔', '🍟'],
      vehicles: ['🚗', '🚀', '✈️', '🚃', '🚢', '🚁', '🚜', '🚲', '🚑', '🚒', '🛩️', '🛸']
    };

    const gameState = {
      active: false,
      targetCount: 1,
      gameMode: 'time-limit',
      timeVal: 30,
      goalVal: 10,
      category: 'symbols',
      targets: [],
      foundCount: 0,
      startTime: 0,
      timeElapsedSeconds: 0,
      timeRemainingSeconds: 0,
      timerInterval: null
    };

    // Web Audio API Sound Synthesizer
    function playSound(type) {
      try {
        const AudioContext = window.AudioContext || window.webkitAudioContext;
        if (!AudioContext) return;
        const ctx = new AudioContext();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.connect(gain);
        gain.connect(ctx.destination);

        if (type === 'correct') {
          osc.type = 'sine';
          osc.frequency.setValueAtTime(523.25, ctx.currentTime);
          osc.frequency.setValueAtTime(659.25, ctx.currentTime + 0.08);
          osc.frequency.setValueAtTime(783.99, ctx.currentTime + 0.16);
          gain.gain.setValueAtTime(0.15, ctx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.3);
          osc.start(ctx.currentTime);
          osc.stop(ctx.currentTime + 0.3);
        } else if (type === 'wrong') {
          osc.type = 'sawtooth';
          osc.frequency.setValueAtTime(200, ctx.currentTime);
          osc.frequency.setValueAtTime(150, ctx.currentTime + 0.1);
          gain.gain.setValueAtTime(0.15, ctx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.25);
          osc.start(ctx.currentTime);
          osc.stop(ctx.currentTime + 0.25);
        } else if (type === 'finish') {
          osc.type = 'triangle';
          osc.frequency.setValueAtTime(523.25, ctx.currentTime);
          osc.frequency.setValueAtTime(659.25, ctx.currentTime + 0.1);
          osc.frequency.setValueAtTime(783.99, ctx.currentTime + 0.2);
          osc.frequency.setValueAtTime(1046.50, ctx.currentTime + 0.3);
          gain.gain.setValueAtTime(0.2, ctx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.6);
          osc.start(ctx.currentTime);
          osc.stop(ctx.currentTime + 0.6);
        }
      } catch (e) {
        // Fallback for audio restrictions
      }
    }

    // --- NAVIGATION LOGIC ---
    function switchTab(tabId) {
      document.getElementById('tab-app').classList.add('hidden');
      document.getElementById('tab-records').classList.add('hidden');
      document.getElementById('tab-print').classList.add('hidden');

      const inactiveClass = "px-3 sm:px-4 py-2 rounded-xl text-slate-600 hover:text-slate-900 transition-all flex items-center space-x-1.5 font-black";
      const activeClass = "px-3 sm:px-4 py-2 rounded-xl text-brand-600 bg-white shadow-xs transition-all flex items-center space-x-1.5 font-black";

      document.getElementById('nav-app-btn').className = inactiveClass;
      document.getElementById('nav-records-btn').className = inactiveClass;
      document.getElementById('nav-print-btn').className = inactiveClass;

      if (tabId === 'app') {
        document.getElementById('tab-app').classList.remove('hidden');
        document.getElementById('nav-app-btn').className = activeClass;
      } else if (tabId === 'records') {
        document.getElementById('tab-records').classList.remove('hidden');
        document.getElementById('nav-records-btn').className = activeClass;
        renderRecordsTable();
      } else if (tabId === 'print') {
        document.getElementById('tab-print').classList.remove('hidden');
        document.getElementById('nav-print-btn').className = activeClass;

        const checkedTarget = document.querySelector('input[name="targetCount"]:checked');
        if (checkedTarget) {
          document.getElementById('ws-target-count').value = checkedTarget.value;
        }
        const gameCat = document.getElementById('shape-category');
        if (gameCat) {
          document.getElementById('ws-category').value = gameCat.value;
        }

        generateWorksheet();
      }
    }

    function toggleModeSettings() {
      const mode = document.querySelector('input[name="gameMode"]:checked').value;
      if (mode === 'time-limit') {
        document.getElementById('setting-time-limit').classList.remove('hidden');
        document.getElementById('setting-speed-run').classList.add('hidden');
      } else {
        document.getElementById('setting-time-limit').classList.add('hidden');
        document.getElementById('setting-speed-run').classList.remove('hidden');
      }
    }

    function updateSetupUI() {
      // Any setup dynamic changes
    }

    // --- GAME ENGINE LOGIC ---
    function startGame() {
      const form = document.getElementById('setup-form');
      gameState.targetCount = parseInt(form.querySelector('input[name="targetCount"]:checked').value);
      gameState.gameMode = form.querySelector('input[name="gameMode"]:checked').value;
      gameState.timeVal = parseInt(form.querySelector('input[name="timeVal"]:checked').value);
      gameState.goalVal = parseInt(form.querySelector('input[name="goalVal"]:checked').value);
      gameState.category = document.getElementById('shape-category').value;

      // Pick target shapes
      const currentShapes = [...SHAPE_SETS[gameState.category]].sort(() => 0.5 - Math.random());
      gameState.targets = currentShapes.slice(0, gameState.targetCount);

      gameState.foundCount = 0;
      gameState.active = true;
      gameState.startTime = Date.now();
      gameState.timeElapsedSeconds = 0;
      gameState.timeRemainingSeconds = gameState.timeVal;

      // UI setup
      document.getElementById('game-setup-card').classList.add('hidden');
      document.getElementById('game-arena').classList.remove('hidden');

      // Targets Display
      document.getElementById('target-display').innerHTML = gameState.targets.map(t => `<span class="animate-pop">${t}</span>`).join(' ');

      // Counter Display
      if (gameState.gameMode === 'speed-run') {
        document.getElementById('counter-goal-suffix').textContent = ` / ${gameState.goalVal}個`;
      } else {
        document.getElementById('counter-goal-suffix').textContent = `個`;
      }
      document.getElementById('game-counter').firstChild.textContent = '0';

      // Generate Interactive Grid Board
      generateGridTiles();

      // Start Timer Interval
      clearInterval(gameState.timerInterval);
      updateTimerUI();

      gameState.timerInterval = setInterval(() => {
        const now = Date.now();
        gameState.timeElapsedSeconds = Math.floor((now - gameState.startTime) / 1000);

        if (gameState.gameMode === 'time-limit') {
          gameState.timeRemainingSeconds = gameState.timeVal - gameState.timeElapsedSeconds;
          if (gameState.timeRemainingSeconds <= 0) {
            gameState.timeRemainingSeconds = 0;
            updateTimerUI();
            finishGame(false);
            return;
          }
        }
        updateTimerUI();
      }, 200);
    }

    function updateTimerUI() {
      const timerEl = document.getElementById('game-timer');
      if (gameState.gameMode === 'time-limit') {
        const secs = gameState.timeRemainingSeconds;
        timerEl.textContent = `00:${secs < 10 ? '0' + secs : secs}`;
        if (secs <= 5) {
          timerEl.className = "text-2xl sm:text-3xl font-black text-rose-500 font-mono animate-pulse";
        } else {
          timerEl.className = "text-2xl sm:text-3xl font-black text-slate-800 font-mono";
        }
      } else {
        const secs = gameState.timeElapsedSeconds;
        timerEl.textContent = `${secs}秒`;
        timerEl.className = "text-2xl sm:text-3xl font-black text-slate-800 font-mono";
      }
    }

    function generateGridTiles() {
      const board = document.getElementById('grid-board');
      board.innerHTML = '';

      const totalTiles = 30; // 5x6
      const allCategoryShapes = SHAPE_SETS[gameState.category];
      const dummyShapes = allCategoryShapes.filter(s => !gameState.targets.includes(s));

      // Guarantee at least 6-8 targets on current field
      const targetCountOnBoard = Math.floor(Math.random() * 3) + 6;
      let tilesData = [];

      for (let i = 0; i < targetCountOnBoard; i++) {
        const randomTarget = gameState.targets[Math.floor(Math.random() * gameState.targets.length)];
        tilesData.push({ shape: randomTarget, isTarget: true });
      }

      for (let i = targetCountOnBoard; i < totalTiles; i++) {
        const randomDummy = dummyShapes[Math.floor(Math.random() * dummyShapes.length)];
        tilesData.push({ shape: randomDummy, isTarget: false });
      }

      // Shuffle tiles
      tilesData.sort(() => 0.5 - Math.random());

      // Render Tile Buttons
      tilesData.forEach((item, index) => {
        const btn = document.createElement('button');
        btn.className = "h-11 sm:h-13 bg-slate-100 hover:bg-slate-200 active:scale-95 border-2 border-slate-200 rounded-xl flex items-center justify-center text-2xl sm:text-3xl shadow-xs transition-all cursor-pointer font-black text-slate-800";
        btn.textContent = item.shape;

        btn.onclick = () => handleTileClick(btn, item);
        board.appendChild(btn);
      });
    }

    function handleTileClick(btn, item) {
      if (!gameState.active || btn.disabled) return;

      if (item.isTarget) {
        // Correct Target Clicked!
        playSound('correct');
        btn.disabled = true;
        btn.className = "h-11 sm:h-13 bg-emerald-100 border-2 border-emerald-400 rounded-xl flex items-center justify-center text-2xl sm:text-3xl opacity-30 cursor-not-allowed transform scale-90 transition-all";
        
        gameState.foundCount++;
        document.getElementById('game-counter').firstChild.textContent = gameState.foundCount;

        // Check if Speed Run goal reached
        if (gameState.gameMode === 'speed-run' && gameState.foundCount >= gameState.goalVal) {
          finishGame(false);
          return;
        }

        // Check if all targets on current board are found; if so, regenerate board dynamically
        const remainingTargets = Array.from(document.getElementById('grid-board').children).filter(child => !child.disabled && gameState.targets.includes(child.textContent));
        if (remainingTargets.length === 0) {
          setTimeout(() => generateGridTiles(), 200);
        }

      } else {
        // Incorrect Target Clicked
        playSound('wrong');
        btn.classList.add('animate-shake', 'bg-rose-100', 'border-rose-400');
        setTimeout(() => {
          btn.classList.remove('animate-shake', 'bg-rose-100', 'border-rose-400');
        }, 300);
      }
    }

    function abortGame() {
      clearInterval(gameState.timerInterval);
      gameState.active = false;
      document.getElementById('game-arena').classList.add('hidden');
      document.getElementById('game-setup-card').classList.remove('hidden');
    }

    function finishGame(isAborted = false) {
      clearInterval(gameState.timerInterval);
      gameState.active = false;
      playSound('finish');

      const totalTimeUsed = ((Date.now() - gameState.startTime) / 1000).toFixed(1);

      // Save Record to LocalStorage
      saveRecord({
        date: new Date().toLocaleString('ja-JP', { month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit' }),
        targetsStr: `${gameState.targetCount}種類 (${gameState.targets.join(' ')})`,
        modeStr: gameState.gameMode === 'time-limit' ? `制限時間 ${gameState.timeVal}秒` : `タイムアタック ${gameState.goalVal}個`,
        foundCount: gameState.foundCount,
        timeUsed: totalTimeUsed,
        gameMode: gameState.gameMode,
        timeVal: gameState.timeVal,
        goalVal: gameState.goalVal
      });

      // Update Modal Text
      document.getElementById('res-mode-label').textContent = `${gameState.targetCount}種類 / ${gameState.gameMode === 'time-limit' ? gameState.timeVal + '秒制限' : '目標 ' + gameState.goalVal + '個'}`;
      document.getElementById('res-found-count').textContent = `${gameState.foundCount} 個`;
      document.getElementById('res-time-value').textContent = `${totalTimeUsed} 秒`;

      // Show Modal
      document.getElementById('game-arena').classList.add('hidden');
      document.getElementById('game-setup-card').classList.remove('hidden');
      document.getElementById('result-modal').classList.remove('hidden');
    }

    function closeResultModal() {
      document.getElementById('result-modal').classList.add('hidden');
    }

    // --- RECORDS & LOCALSTORAGE LOGIC ---
    function saveRecord(newRecord) {
      const records = JSON.parse(localStorage.getItem('shape_search_records') || '[]');
      records.unshift(newRecord);
      localStorage.setItem('shape_search_records', JSON.stringify(records.slice(0, 30)));
    }

    function renderRecordsTable() {
      const records = JSON.parse(localStorage.getItem('shape_search_records') || '[]');
      const tbody = document.getElementById('history-table-body');
      const noHistoryMsg = document.getElementById('no-history-msg');

      tbody.innerHTML = '';

      if (records.length === 0) {
        noHistoryMsg.classList.remove('hidden');
        document.getElementById('best-30s').textContent = '-- 個';
        document.getElementById('best-15s').textContent = '-- 個';
        document.getElementById('best-10item').textContent = '-- 秒';
        return;
      }

      noHistoryMsg.classList.add('hidden');

      // Calculate High Scores
      let max30s = 0;
      let max15s = 0;
      let min10item = Infinity;

      records.forEach(r => {
        if (r.gameMode === 'time-limit' && r.timeVal === 30) {
          if (r.foundCount > max30s) max30s = r.foundCount;
        }
        if (r.gameMode === 'time-limit' && r.timeVal === 15) {
          if (r.foundCount > max15s) max15s = r.foundCount;
        }
        if (r.gameMode === 'speed-run' && r.goalVal === 10) {
          const t = parseFloat(r.timeUsed);
          if (t < min10item) min10item = t;
        }

        // Render Table Row
        const tr = document.createElement('tr');
        tr.className = "hover:bg-slate-50 transition-all";
        tr.innerHTML = `
          <td class="py-3 px-3 text-slate-500 font-normal text-xs">${r.date}</td>
          <td class="py-3 px-3">${r.targetsStr}</td>
          <td class="py-3 px-3">${r.modeStr}</td>
          <td class="py-3 px-3 text-right font-black text-brand-600 text-base">
            ${r.gameMode === 'time-limit' ? r.foundCount + ' 個' : r.timeUsed + ' 秒'}
          </td>
        `;
        tbody.appendChild(tr);
      });

      document.getElementById('best-30s').textContent = max30s > 0 ? `${max30s} 個` : '-- 個';
      document.getElementById('best-15s').textContent = max15s > 0 ? `${max15s} 個` : '-- 個';
      document.getElementById('best-10item').textContent = min10item !== Infinity ? `${min10item} 秒` : '-- 秒';
    }

    function confirmClearHistory() {
      if (window.confirm ? confirm('過去のすべてのプレイ記録を消去しますか？') : true) {
        localStorage.removeItem('shape_search_records');
        renderRecordsTable();
      }
    }

    // --- PRINTABLE WORKSHEET GENERATOR LOGIC ---
    function generateWorksheet() {
      const targetCountEl = document.getElementById('ws-target-count');
      const targetCount = targetCountEl ? parseInt(targetCountEl.value) : 1;
      const totalGridItems = parseInt(document.getElementById('ws-grid-size').value);
      const category = document.getElementById('ws-category') ? document.getElementById('ws-category').value : 'symbols';

      const shapes = SHAPE_SETS[category] || SHAPE_SETS.symbols;
      const shuffled = [...shapes].sort(() => 0.5 - Math.random());
      const targets = shuffled.slice(0, targetCount);
      const dummies = shapes.filter(s => !targets.includes(s));

      // Display Target Box with 1 or 2 target badges
      const targetBox = document.getElementById('ws-target-box');
      if (targetCount === 1) {
        targetBox.innerHTML = `
          <div class="flex items-center space-x-2">
            <span class="inline-block px-5 py-2 bg-amber-50 border-2 border-slate-800 rounded-2xl font-black text-3xl sm:text-4xl text-amber-600">${targets[0]}</span>
          </div>
        `;
      } else {
        targetBox.innerHTML = `
          <div class="flex items-center space-x-3">
            <div class="flex items-center space-x-1.5 border-2 border-slate-800 bg-amber-50 px-3.5 py-1.5 rounded-2xl">
              <span class="text-xs font-black text-amber-800 bg-amber-200 px-1.5 py-0.5 rounded-md">①</span>
              <span class="text-3xl sm:text-4xl font-black text-amber-600">${targets[0]}</span>
            </div>
            <span class="text-base font-black text-slate-600">と</span>
            <div class="flex items-center space-x-1.5 border-2 border-slate-800 bg-amber-50 px-3.5 py-1.5 rounded-2xl">
              <span class="text-xs font-black text-amber-800 bg-amber-200 px-1.5 py-0.5 rounded-md">②</span>
              <span class="text-3xl sm:text-4xl font-black text-amber-600">${targets[1]}</span>
            </div>
          </div>
        `;
      }

      // Build Grid
      const gridBoard = document.getElementById('ws-grid-board');
      
      let gridColsClass = "grid-cols-6";
      if (totalGridItems === 30) gridColsClass = "grid-cols-5";
      if (totalGridItems === 56) gridColsClass = "grid-cols-7";

      gridBoard.className = `grid ${gridColsClass} gap-2.5 sm:gap-3 py-4 border-2 border-slate-800 rounded-2xl p-3 sm:p-4 bg-white text-center`;
      gridBoard.innerHTML = '';

      // Guarantee targets on worksheet grid (balanced quantity of target shapes)
      const targetQuantity = Math.floor(totalGridItems * 0.36);
      let items = [];

      for (let i = 0; i < targetQuantity; i++) {
        items.push(targets[i % targets.length]);
      }
      for (let i = targetQuantity; i < totalGridItems; i++) {
        items.push(dummies[Math.floor(Math.random() * dummies.length)]);
      }

      // Shuffle worksheet items
      items.sort(() => 0.5 - Math.random());

      // Render Grid Items
      items.forEach(shape => {
        const itemDiv = document.createElement('div');
        itemDiv.className = "h-12 sm:h-14 flex items-center justify-center border border-slate-300 rounded-xl text-2xl sm:text-3xl font-black text-slate-800 bg-slate-50/50";
        itemDiv.textContent = shape;
        gridBoard.appendChild(itemDiv);
      });
    }

    // Initialize View on Load
    window.onload = function() {
      toggleModeSettings();
      renderRecordsTable();
    };
  </script>
</body>
</html>
