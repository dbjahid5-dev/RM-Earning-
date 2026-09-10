<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Telegram Earning Mini App</title>
    
    <!-- Tailwind CSS for Modern UI -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Telegram Web App SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>

    <style>
        /* Telegram Dynamic Theme Variables */
        body { 
            background-color: var(--tg-theme-bg-color, #0f172a); 
            color: var(--tg-theme-text-color, #ffffff); 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }
        .card { background-color: var(--tg-theme-secondary-bg-color, #1e293b); }
        .btn-primary { background-color: var(--tg-theme-button-color, #3b82f6); color: var(--tg-theme-button-text-color, #ffffff); }
        .bottom-nav { background-color: var(--tg-theme-secondary-bg-color, #1e293b); border-top: 1px solid #334155; }
        
        .page { display: none; padding-bottom: 90px; }
        .page.active { display: block; animation: fadeIn 0.3s ease-in-out; }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="antialiased min-h-screen relative selection:bg-blue-500 selection:text-white">

    <div id="app" class="p-4 max-w-md mx-auto">
        
        <!-- ================= PAGE: HOME / WALLET ================= -->
        <div id="home" class="page active">
            <div class="flex justify-between items-center mb-6 mt-2">
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 rounded-full bg-blue-500 flex items-center justify-center text-xl font-bold">
                        <i class="fa-solid fa-user"></i>
                    </div>
                    <h2 class="text-lg font-bold" id="userName">User Name</h2>
                </div>
                <div class="text-xs bg-gray-700 px-3 py-1 rounded-full text-gray-300">
                    ID: <span id="userId">123456</span>
                </div>
            </div>
            
            <!-- Balance Card -->
            <div class="card rounded-3xl p-6 mb-6 text-center shadow-lg border border-gray-700 relative overflow-hidden">
                <div class="absolute top-0 right-0 w-32 h-32 bg-blue-500 rounded-full mix-blend-multiply filter blur-3xl opacity-20"></div>
                <p class="text-gray-400 text-sm uppercase tracking-wider font-semibold mb-1">Available Balance</p>
                <h1 class="text-5xl font-extrabold text-green-400">RM <span id="balance">0.00</span></h1>
                <div class="mt-4 pt-4 border-t border-gray-600 flex justify-between px-2">
                    <div>
                        <p class="text-gray-400 text-xs">Total Earned</p>
                        <p class="font-bold text-sm">RM <span id="totalEarned">0.00</span></p>
                    </div>
                    <div>
                        <p class="text-gray-400 text-xs">Ad Earnings</p>
                        <p class="font-bold text-sm">RM <span id="adEarned">0.00</span></p>
                    </div>
                </div>
            </div>

            <!-- Action Buttons -->
            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="watchAd()" class="btn-primary py-5 rounded-2xl font-bold shadow-lg shadow-blue-500/30 flex flex-col items-center transition active:scale-95">
                    <i class="fa-solid fa-play-circle mb-2 text-3xl"></i>
                    <span>Watch Ad</span>
                    <span class="text-xs font-normal opacity-80">+ RM 0.02</span>
                </button>
                <button onclick="nav('withdraw')" class="card border border-gray-600 hover:bg-gray-700 py-5 rounded-2xl font-bold shadow-lg flex flex-col items-center transition active:scale-95">
                    <i class="fa-solid fa-money-bill-transfer mb-2 text-3xl text-green-400"></i>
                    <span>Withdraw</span>
                    <span class="text-xs text-gray-400 font-normal">Min RM 50</span>
                </button>
            </div>
            
            <div class="text-center text-xs text-gray-500 flex items-center justify-center space-x-1">
                <i class="fa-solid fa-shield-halved"></i>
                <span>Ads powered by Monetag</span>
            </div>
        </div>

        <!-- ================= PAGE: TASKS ================= -->
        <div id="tasks" class="page">
            <h2 class="text-2xl font-extrabold mb-2">Missions 🎯</h2>
            <p class="text-gray-400 text-sm mb-6">Complete tasks to earn extra RM.</p>
            
            <div id="taskList" class="space-y-4">
                <!-- Task Items will be injected by JS -->
            </div>
        </div>

        <!-- ================= PAGE: REFERRAL ================= -->
        <div id="referral" class="page">
            <h2 class="text-2xl font-extrabold mb-2">Invite Friends 👥</h2>
            <p class="text-gray-400 text-sm mb-6">Earn RM 0.30 for every valid referral.</p>
            
            <div class="card rounded-2xl p-6 text-center mb-6 border border-gray-700 shadow-lg">
                <div class="w-16 h-16 bg-blue-500/20 rounded-full flex items-center justify-center mx-auto mb-3">
                    <i class="fa-solid fa-users text-3xl text-blue-400"></i>
                </div>
                <div class="grid grid-cols-2 gap-4 mt-4">
                    <div class="bg-gray-800 p-3 rounded-xl">
                        <p class="text-xs text-gray-400">Total Referrals</p>
                        <p class="text-xl font-bold" id="refCount">0</p>
                    </div>
                    <div class="bg-gray-800 p-3 rounded-xl">
                        <p class="text-xs text-gray-400">Ref Earnings</p>
                        <p class="text-xl font-bold text-green-400">RM <span id="refEarnings">0.00</span></p>
                    </div>
                </div>
            </div>

            <div class="card p-2 rounded-xl border border-gray-700 flex items-center space-x-2">
                <input type="text" id="refLink" readonly class="w-full bg-transparent text-white p-3 text-sm focus:outline-none" value="https://t.me/YourBot?start=123456">
                <button onclick="copyRef()" class="btn-primary p-3 px-5 rounded-lg font-bold shadow-md active:scale-95 transition">
                    Copy
                </button>
            </div>
        </div>

        <!-- ================= PAGE: LEADERBOARD ================= -->
        <div id="leaderboard" class="page">
            <h2 class="text-2xl font-extrabold mb-2">Top Earners 🏆</h2>
            <p class="text-gray-400 text-sm mb-6">The most active users.</p>
            
            <div class="card rounded-2xl border border-gray-700 overflow-hidden">
                <ul id="lbList" class="divide-y divide-gray-700">
                    <!-- Leaderboard Items injected by JS -->
                </ul>
            </div>
        </div>

        <!-- ================= PAGE: WITHDRAW ================= -->
        <div id="withdraw" class="page">
            <h2 class="text-2xl font-extrabold mb-2">Withdraw Funds 💵</h2>
            <p class="text-gray-400 text-sm mb-6">Transfer money to your account.</p>
            
            <div class="card rounded-2xl p-6 border border-gray-700 shadow-lg">
                <div class="flex justify-between items-center mb-6 bg-gray-800 p-4 rounded-xl">
                    <span class="text-gray-400 text-sm">Your Balance:</span>
                    <span class="font-bold text-green-400 text-lg">RM <span id="wdAvailBalance">0.00</span></span>
                </div>

                <label class="block text-sm text-gray-400 mb-1">Amount (Min RM 50.00)</label>
                <div class="relative mb-4">
                    <span class="absolute left-4 top-3 text-gray-400 font-bold">RM</span>
                    <input type="number" id="wdAmount" placeholder="0.00" class="w-full bg-gray-800 p-3 pl-12 rounded-xl border border-gray-600 text-white focus:border-blue-500 focus:outline-none">
                </div>

                <label class="block text-sm text-gray-400 mb-1">Payment Method</label>
                <select id="wdMethod" class="w-full bg-gray-800 p-3 rounded-xl mb-4 border border-gray-600 text-white focus:outline-none">
                    <option value="Bank">Bank Transfer</option>
                    <option value="TNG">Touch 'n Go eWallet</option>
                    <option value="Boost">Boost Wallet</option>
                </select>

                <label class="block text-sm text-gray-400 mb-1">Account Number / Phone</label>
                <input type="text" id="wdDetails" placeholder="Enter details..." class="w-full bg-gray-800 p-3 rounded-xl mb-6 border border-gray-600 text-white focus:border-blue-500 focus:outline-none">
                
                <button onclick="requestWithdraw()" class="w-full btn-primary py-4 rounded-xl font-bold shadow-lg shadow-blue-500/30 active:scale-95 transition">
                    Submit Request
                </button>
            </div>
        </div>

    </div>

    <!-- ================= BOTTOM NAVIGATION ================= -->
    <div class="fixed bottom-0 w-full max-w-md left-1/2 transform -translate-x-1/2 bottom-nav flex justify-between items-center px-2 py-3 z-50 pb-safe">
        <button onclick="nav('home')" class="nav-btn active-nav text-blue-400 flex flex-col items-center w-1/5 transition-colors">
            <i class="fa-solid fa-house text-xl mb-1"></i><span class="text-[10px] font-medium">Home</span>
        </button>
        <button onclick="nav('tasks')" class="nav-btn text-gray-500 flex flex-col items-center w-1/5 transition-colors">
            <i class="fa-solid fa-list-check text-xl mb-1"></i><span class="text-[10px] font-medium">Tasks</span>
        </button>
        <button onclick="nav('referral')" class="nav-btn text-gray-500 flex flex-col items-center w-1/5 transition-colors">
            <i class="fa-solid fa-user-plus text-xl mb-1"></i><span class="text-[10px] font-medium">Referral</span>
        </button>
        <button onclick="nav('leaderboard')" class="nav-btn text-gray-500 flex flex-col items-center w-1/5 transition-colors">
            <i class="fa-solid fa-trophy text-xl mb-1"></i><span class="text-[10px] font-medium">Leaders</span>
        </button>
        <button onclick="nav('withdraw')" class="nav-btn text-gray-500 flex flex-col items-center w-1/5 transition-colors">
            <i class="fa-solid fa-wallet text-xl mb-1"></i><span class="text-[10px] font-medium">Wallet</span>
        </button>
    </div>

    <!-- ================= APPLICATION LOGIC (JS) ================= -->
    <script>
        // Telegram Web App Initialization
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.ready();

        // ---------------------------------------------------------
        // 1. DATA MANAGEMENT (Simulated with LocalStorage for Single HTML Testing)
        // Note: In production, fetch this from your Node.js/SQLite backend
        // ---------------------------------------------------------
        let user = JSON.parse(localStorage.getItem('tg_earning_user')) || {
            id: tg.initDataUnsafe?.user?.id || Math.floor(Math.random() * 1000000),
            username: tg.initDataUnsafe?.user?.username || tg.initDataUnsafe?.user?.first_name || "Guest User",
            balance: 0.00,
            total_earned: 0.00,
            ad_earned: 0.00,
            ref_count: 0,
            ref_earnings: 0.00
        };

        const config = {
            ad_reward: 0.02,
            ref_reward: 0.30,
            min_withdraw: 50.00
        };

        // Pre-defined Tasks
        let tasks = JSON.parse(localStorage.getItem('tg_tasks')) || [
            { id: 1, title: "Join Official Channel", reward: 0.50, link: "https://t.me/telegram", completed: false },
            { id: 2, title: "Follow on Twitter", reward: 0.30, link: "https://twitter.com", completed: false },
            { id: 3, title: "Subscribe YouTube", reward: 1.00, link: "https://youtube.com", completed: false }
        ];

        // Fake Leaderboard Data
        const leaderboard = [
            { name: "Alihassan", earned: 450.50 },
            { name: "SitiNur", earned: 320.20 },
            { name: "Ahmad99", earned: 210.00 },
            { name: "Kamal_Bro", earned: 180.40 },
            { name: "Nina_22", earned: 150.00 }
        ];

        function saveData() {
            localStorage.setItem('tg_earning_user', JSON.stringify(user));
            localStorage.setItem('tg_tasks', JSON.stringify(tasks));
        }

        // ---------------------------------------------------------
        // 2. UI UPDATES & NAVIGATION
        // ---------------------------------------------------------
        function updateUI() {
            document.getElementById('userName').innerText = user.username;
            document.getElementById('userId').innerText = user.id;
            
            document.getElementById('balance').innerText = user.balance.toFixed(2);
            document.getElementById('totalEarned').innerText = user.total_earned.toFixed(2);
            document.getElementById('adEarned').innerText = user.ad_earned.toFixed(2);
            
            document.getElementById('refCount').innerText = user.ref_count;
            document.getElementById('refEarnings').innerText = user.ref_earnings.toFixed(2);
            
            document.getElementById('wdAvailBalance').innerText = user.balance.toFixed(2);
            document.getElementById('refLink').value = `https://t.me/YourBotUsername?start=${user.id}`;
        }

        function nav(pageId) {
            // Switch Pages
            document.querySelectorAll('.page').forEach(el => el.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
            
            // Update Bottom Nav Colors
            document.querySelectorAll('.nav-btn').forEach(el => {
                el.classList.remove('text-blue-400');
                el.classList.add('text-gray-500');
            });
            event.currentTarget.classList.remove('text-gray-500');
            event.currentTarget.classList.add('text-blue-400');

            // Trigger specific page renders
            if(pageId === 'tasks') renderTasks();
            if(pageId === 'leaderboard') renderLeaderboard();
            
            // Telegram Haptic Feedback
            tg.HapticFeedback.impactOccurred('light');
        }

        // ---------------------------------------------------------
        // 3. CORE FEATURES (Ads, Tasks, Withdraw, Referral)
        // ---------------------------------------------------------

        // Copy Referral
        function copyRef() {
            const link = document.getElementById('refLink');
            link.select();
            document.execCommand('copy');
            tg.HapticFeedback.notificationOccurred('success');
            tg.showAlert("✅ Referral link copied to clipboard!");
        }

        // Watch Ad (Monetag Simulation)
        function watchAd() {
            tg.showConfirm("Watch an ad to earn RM 0.02?", (confirmed) => {
                if(confirmed) {
                    // Show a simple loading state (Simulating Ad Loading/Viewing)
                    tg.MainButton.text = "Watching Ad...";
                    tg.MainButton.show();
                    tg.MainButton.showProgress();

                    setTimeout(() => {
                        // Monetag SDK integration goes here in production
                        user.balance += config.ad_reward;
                        user.total_earned += config.ad_reward;
                        user.ad_earned += config.ad_reward;
                        saveData();
                        updateUI();
                        
                        tg.MainButton.hide();
                        tg.HapticFeedback.notificationOccurred('success');
                        tg.showAlert("🎉 Success! RM 0.02 has been added to your balance.");
                    }, 3000); // 3 seconds fake ad duration
                }
            });
        }

        // Render Tasks
        function renderTasks() {
            const list = document.getElementById('taskList');
            list.innerHTML = '';
            
            tasks.forEach((task, index) => {
                list.innerHTML += `
                    <div class="card rounded-2xl p-4 flex justify-between items-center border border-gray-700 shadow-md">
                        <div class="flex items-center space-x-3">
                            <div class="w-10 h-10 rounded-full bg-blue-500/20 text-blue-400 flex items-center justify-center font-bold">
                                <i class="fa-solid fa-gift"></i>
                            </div>
                            <div>
                                <h4 class="font-bold text-sm">${task.title}</h4>
                                <p class="text-xs text-green-400 font-semibold">+ RM ${task.reward.toFixed(2)}</p>
                            </div>
                        </div>
                        ${task.completed ? 
                            `<button class="bg-gray-700 text-gray-400 px-4 py-2 rounded-xl text-xs font-bold cursor-not-allowed"><i class="fa-solid fa-check"></i> Done</button>` : 
                            `<button onclick="startTask(${index})" class="btn-primary px-4 py-2 rounded-xl text-xs font-bold shadow-md active:scale-95 transition">Start</button>`
                        }
                    </div>
                `;
            });
        }

        // Complete Task
        function startTask(index) {
            const task = tasks[index];
            tg.openLink(task.link); // Open link in browser/telegram
            
            // Simulate task verification after 5 seconds
            setTimeout(() => {
                tg.showConfirm(`Did you complete: "${task.title}"?`, (confirmed) => {
                    if (confirmed) {
                        tasks[index].completed = true;
                        user.balance += task.reward;
                        user.total_earned += task.reward;
                        saveData();
                        updateUI();
                        renderTasks();
                        tg.HapticFeedback.notificationOccurred('success');
                        tg.showAlert(`✅ Task Completed! RM ${task.reward.toFixed(2)} added.`);
                    }
                });
            }, 3000);
        }

        // Render Leaderboard
        function renderLeaderboard() {
            const list = document.getElementById('lbList');
            list.innerHTML = '';
            leaderboard.forEach((u, i) => {
                let medal = i === 0 ? '<span class="text-2xl">🥇</span>' : i === 1 ? '<span class="text-2xl">🥈</span>' : i === 2 ? '<span class="text-2xl">🥉</span>' : `<span class="w-6 text-center text-gray-500 font-bold">${i+1}</span>`;
                list.innerHTML += `
                    <li class="p-4 flex justify-between items-center bg-gray-800/30 hover:bg-gray-800 transition">
                        <div class="flex items-center space-x-3">
                            ${medal}
                            <span class="font-bold text-sm text-gray-200">${u.name}</span>
                        </div>
                        <span class="text-green-400 font-extrabold text-sm">RM ${u.earned.toFixed(2)}</span>
                    </li>
                `;
            });
        }

        // Request Withdraw
        function requestWithdraw() {
            const amount = parseFloat(document.getElementById('wdAmount').value);
            const method = document.getElementById('wdMethod').value;
            const details = document.getElementById('wdDetails').value;

            if (isNaN(amount) || amount <= 0) {
                return tg.showAlert("❌ Please enter a valid amount.");
            }
            if (amount < config.min_withdraw) {
                return tg.showAlert(`❌ Minimum withdrawal is RM ${config.min_withdraw.toFixed(2)}`);
            }
            if (amount > user.balance) {
                return tg.showAlert("❌ Insufficient balance.");
            }
            if (!details.trim()) {
                return tg.showAlert("❌ Please enter account details.");
            }

            // Process withdrawal
            user.balance -= amount;
            saveData();
            updateUI();
            
            document.getElementById('wdAmount').value = '';
            document.getElementById('wdDetails').value = '';

            tg.HapticFeedback.notificationOccurred('success');
            tg.showAlert(`✅ Withdrawal Request for RM ${amount.toFixed(2)} via ${method} submitted successfully! Please wait for admin approval.`);
        }

        // Initialize App
        updateUI();

    </script>
</body>
</html>
