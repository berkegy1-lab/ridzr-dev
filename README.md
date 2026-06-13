<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RIDZR 360° — TRANSFORMATION PLATFORM</title>
    
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="RIDZR 360">
    <link rel="manifest" href="data:application/manifest+json,{%22name%22:%22RIDZR%20360%20Transformation%22,%22short_name%22:%22RIDZR360%22,%22start_url%22:%22.%22,%22display%22:%22standalone%22,%22background_color%22:%22%23000000%22,%22theme_color%22:%22%23000000%22}">

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+Arabic:wght@300;400;600&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --font-ar: 'IBM Plex Sans Arabic', sans-serif;
            --font-en: 'Courier New', monospace;
            --accent: #ff3333;
        }

        body {
            font-family: var(--font-ar);
            background-color: black;
            color: white;
            margin: 0;
            padding: 0;
            text-align: center;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            overflow-y: auto;
        }

        .screen {
            display: none;
            width: 100%;
            max-width: 650px;
            padding: 20px;
            box-sizing: border-box;
        }
        .active { display: block; }

        .en-brand { font-family: var(--font-en); font-weight: bold; letter-spacing: 2px; }

        button {
            background-color: transparent;
            border: 1px solid white;
            color: white;
            padding: 12px 24px;
            margin: 10px;
            cursor: pointer;
            font-size: 16px;
            font-family: inherit;
            transition: all 0.3s ease;
        }
        button:hover { background-color: white; color: black; }
        
        .btn-premium { border-color: var(--accent); color: var(--accent); font-weight: bold; }
        .btn-premium:hover { background-color: var(--accent); color: white; }

        input, textarea {
            background-color: black; color: white; border: 1px solid white;
            padding: 14px; margin: 10px 0; width: 100%; max-width: 400px; box-sizing: border-box;
            font-family: inherit; font-size: 16px; text-align: center;
        }

        /* بنر تثبيت الـ PWA */
        .pwa-banner {
            display: none; background: #111; border: 1px solid #333; padding: 15px;
            position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%);
            width: 90%; max-width: 450px; z-index: 9999; text-align: center;
        }

        /* صندوق جدار الدفع التسويقي */
        .paywall-box {
            background: #0d0d0d; border: 1px solid #222; padding: 40px;
            box-shadow: 0 0 30px rgba(255, 51, 51, 0.1); margin-top: 20px;
        }

        /* شريط التقدم والأرقام */
        .progress-container { width: 100%; background-color: #222; height: 8px; margin: 20px 0; border-radius: 4px; overflow: hidden; }
        .progress-bar { height: 100%; background-color: white; width: 0%; transition: width 0.5s ease; }

        /* ستايل قائمة المهام */
        .task-list { list-style: none; padding: 0; margin: 20px 0; text-align: right; }
        .task-item { background: #111; border: 1px solid #222; padding: 15px; margin-bottom: 10px; }
        .task-row { display: flex; justify-content: space-between; align-items: center; }
        .task-item input[type="checkbox"] { width: 20px; height: 20px; accent-color: white; cursor: pointer; }
        .task-item label { flex-grow: 1; margin-right: 15px; cursor: pointer; }
        
        .proof-section { margin-top: 10px; padding-top: 10px; border-top: 1px dashed #333; display: flex; align-items: center; gap: 10px; }
        .proof-btn { font-size: 11px; background: #000; border: 1px dashed #555; padding: 3px 8px; cursor: pointer; color: #aaa; }
        .proof-status { font-size: 11px; color: #00ffcc; display: none; }

        .completed-task label { text-decoration: line-through; color: #555; }
        
        /* بار الإحصائيات العلوي */
        .top-stats { display: flex; justify-content: space-between; background: #111; padding: 10px 20px; margin-bottom: 20px; font-size: 14px; border: 1px solid #222; }

        .avatar-container { display: flex; justify-content: center; gap: 15px; margin: 15px 0; }
        .avatar-option { width: 50px; height: 50px; border: 1px solid #444; display: flex; justify-content: center; align-items: center; font-size: 22px; cursor: pointer; transition: all 0.3s ease; }
        .avatar-option.selected { border-color: white; background-color: #222; transform: scale(1.1); }

        @keyframes confetti {
            0% { transform: translateY(-10px) rotate(0deg); opacity: 1; }
            100% { transform: translateY(100vh) rotate(360deg); opacity: 0; }
        }
        .pop { position: fixed; width: 8px; height: 8px; background: #fff; top: -10px; z-index: 999; animation: confetti 2s linear forwards; }
    </style>
</head>
<body>

    <div id="pwa-install-msg" class="pwa-banner">
        <p style="margin: 0 0 10px 0; font-size: 13px;">⚡ ثبت تطبيق RIDZR 360 الآن على شاشتك الرئيسية للوصول السريع!</p>
        <button onclick="installAppPWA()" class="btn-premium" style="padding: 5px 15px; margin: 0; font-size: 12px;">تثبيت التطبيق 📱</button>
        <button onclick="closePWABanner()" style="padding: 5px 15px; margin-left: 5px; font-size: 12px; border-color: #444; color: #aaa;">لاحقاً</button>
    </div>

    <div id="paywall" class="screen active">
        <h1 class="en-brand" style="font-size: 36px; margin-bottom: 5px;">RIDZR 360°</h1>
        <p style="color: #666; font-size: 13px; margin: 0;">نظام التحدي والتحول البدني الكامل</p>
        <div class="paywall-box">
            <h3 style="margin-top:0;">تفعيل التحدي المجاني 🔓</h3>
            <p style="color: #999; font-size: 13px; line-height: 1.6;">للحصول على كود التفعيل الفوري والبدء، سيب تعليق على الفيديو وادخل الخاص (DM) وهيتبعتلك الكود علطول!</p>
            <input type="text" id="pass-code" placeholder="أدخل كود التفعيل اللي جالك في الـ DM">
            <br>
            <button onclick="checkAccessCode()" class="btn-premium" style="width: 100%; margin-top: 10px;">ادخل كود التفعيل وابدأ التحدي ⚡</button>
            <p style="color: #444; font-size: 11px; margin-top: 15px;">(كود التجربة الحالي للمعاينة هو: <strong style="color:#777;">RIDZR10</strong>)</p>
        </div>
    </div>

    <div id="arrival" class="screen">
        <h1 class="en-brand">RIDZR 360°</h1>
        <p>مرحباً بك في أقوى تحدي لصناعة النسخة الأفضل من جسمك وعاداتك اليومية.</p>
        <button onclick="goToIdentityScreen()">يلا بينا نبدأ 🔥</button>
    </div>

    <div id="identity" class="screen">
        <h2>اكتب اسمك واختار شخصيتك للتسلية 😎</h2>
        <input type="text" id="user-nickname" placeholder="اكتب اسمك أو لقبك هنا...">
        
        <p style="color: #aaa; font-size: 14px;">اختر الأوفاتار بتاعك:</p>
        <div class="avatar-container">
            <div class="avatar-option" onclick="selectAvatar(this, '🥷')">🥷</div>
            <div class="avatar-option" onclick="selectAvatar(this, '🦾')">🦾</div>
            <div class="avatar-option" onclick="selectAvatar(this, '⚡')">⚡</div>
            <div class="avatar-option" onclick="selectAvatar(this, '👑')">👑</div>
        </div>
        
        <button onclick="saveUserIdentity()" style="margin-top: 15px;">تفعيل الشخصية ودخول التحدي ⚡</button>
    </div>

    <div id="phases" class="screen">
        <h2>اختر مدة التحدي اللي هتقدر عليها:</h2>
        <p style="color: #aaa; font-size: 14px;">كل مرحلة ليها نظام وهدف تراكمي خاص بيك:</p>
        <button onclick="selectPhase('تحدي الـ 10 أيام 🟢', 10)">🟢 تحدي الالتزام السريع (10 أيام)</button>
        <br>
        <button onclick="selectPhase('تحدي الـ 30 يوم 🟡', 30)">🟡 تحدي المحارب المتقدم (30 يوم)</button>
        <br>
        <button onclick="selectPhase('تحدي الـ 90 يوم 🔴', 90)">🔴 تحدي التحول الجذري (90 يوم)</button>
    </div>

    <div id="challenges" class="screen">
        
        <div class="top-stats">
            <div>اللاعب: <span id="stat-avatar">🥷</span> <strong id="stat-name" style="color: #00ffcc;">-</strong></div>
            <div>المستوى: <strong id="stat-phase" style="color: gold;">-</strong></div>
            <div>🏆 النقاط: <strong id="stat-score" style="color: #ff3333;">0</strong></div>
        </div>

        <h2>الأيام المتبقية: <span id="days-left" style="color: var(--accent);">0</span> يوم</h2>
        
        <div class="progress-container">
            <div id="progress-bar" class="progress-bar"></div>
        </div>

        <div style="background: #0d0d0d; border: 1px dashed #444; padding: 10px; margin-bottom: 20px;">
            <p style="margin: 0 0 5px 0; font-size: 12px; color: #aaa;">⚙️ زر محاكاة الأيام (عشان تقلب الأيام وتجرب الأبلكيشن بنفسك):</p>
            <button onclick="simulateDayProgress()" style="font-size: 12px; padding: 6px 12px; margin: 0; background: #fff; color: #000; font-weight: bold;">🔄 اضغط هنا للعبور لليوم التالي فوراً</button>
        </div>

        <h3 style="text-align: right; color: #888; font-size: 14px; margin-bottom: 5px;">📋 علم على المهام اللي خلصتها النهاردة:</h3>
        
        <ul id="challenge-list" class="task-list">
            </ul>

        <button onclick="resetPlatformData()" style="font-size: 11px; border-color: #444; color: #666; margin-top: 30px;">إعادة تعيين وبدء التحدي من الأول 🔄</button>
    </div>

    <div id="final" class="screen">
        <h2 class="en-brand" style="color: gold;">CHALLENGE COMPLETED 🎉</h2>
        <p>عاش يا بطل! أتممت التحدي بنجاح والتزام أسطوري.</p>
        <p>اكتب دلوقتي تقريرك النهائي (تطور وزنك، شكل جسمك، أو قوتك في التمرين) عشان نبعتها للكابتن على الواتساب فوراً ويراجع معاك النتيجة:</p>
        <textarea id="return-sentence" rows="5" placeholder="اكتب مذكراتك ونتيجتك هنا (مثال: وزني بقى كذا وقوة تحملي زادت)..."></textarea>
        <br>
        <button onclick="sendReportToWhatsApp()" class="btn-premium" style="width: 100%; margin-top: 10px; background-color: #25d366; color: black; border-color: #25d366;">📱 إرسال النتيجة للكابتن عبر الواتساب وبدء المتابعة</button>
    </div>

    <div id="after" class="screen">
        <h2 class="en-brand">DONE</h2>
        <p>تم إرسال تقريرك وحفظ نتيجتك بنجاح طوال فترة التحدي الحالية.</p>
        <button onclick="resetPlatformData()">دخول تحدي جديد 🔥</button>
    </div>

    <script>
        // قائمة المهام الـ 9 الاحترافية 
        const coreTasks = [
            "🕌 صلاة الفجر + الصلوات الخمس في وقتها",
            "📚 جلسة المذاكرة / التطوير الأولى (تركيز كامل)",
            "📚 جلسة المذاكرة / التطوير الثانية",
            "🚶‍♂️ مشي يومي مستمر من 15 إلى 20 دقيقة",
            "🏋️‍♂️ تمرين الجيم الرياضي (Push-ups / Squats / Plank)",
            "🍗 التغذية الرياضية: إدخال 2500+ سعرة حرارية",
            "🥚 تناول نسبة البروتين اليومية (80-95 جرام بروتين)",
            "💧 شرب 2.5 لتر ماء أو أكثر على مدار اليوم",
            "😴 الاستشفاء البدني: نوم عميق من 7.5 إلى 8.5 ساعات"
        ];

        let appState = {
            isPaidUnlocked: false,
            nickname: '',
            avatar: '🥷',
            phaseName: '',
            totalDuration: 10,
            currentDay: 0,
            score: 0,
            completedTasksOfCurrentDay: []
        };

        let pwaInstallEvent = null;

        // تشغيل نظام الـ PWA للوصول السريع
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            pwaInstallEvent = e;
            document.getElementById('pwa-install-msg').style.display = 'block';
        });

        function installAppPWA() {
            if (pwaInstallEvent) {
                pwaInstallEvent.prompt();
                pwaInstallEvent.userChoice.then(() => { closePWABanner(); });
            }
        }
        function closePWABanner() { document.getElementById('pwa-install-msg').style.display = 'none'; }

        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        function checkAccessCode() {
            let code = document.getElementById('pass-code').value.trim();
            if (code.toUpperCase() === "RIDZR10") {
                appState.isPaidUnlocked = true;
                saveAppState();
                showScreen('arrival');
            } else {
                alert("❌ الكود غلط! روح على الـ DM واكتب تعليق عشان يوصلك الكود الفعال فوراً.");
            }
        }

        function goToIdentityScreen() { showScreen('identity'); }

        function selectAvatar(element, avatarChar) {
            document.querySelectorAll('.avatar-option').forEach(opt => opt.classList.remove('selected'));
            element.classList.add('selected');
            appState.avatar = avatarChar;
        }

        function saveUserIdentity() {
            let name = document.getElementById('user-nickname').value.trim();
            if (name === '') { alert('من فضلك اكتب اسمك أو لقبك عشان تتابع بيه.'); return; }
            appState.nickname = name;
            saveAppState();
            showScreen('phases');
        }

        function selectPhase(name, days) {
            appState.phaseName = name;
            appState.totalDuration = days;
            appState.currentDay = 0;
            appState.score = 0;
            appState.completedTasksOfCurrentDay = new Array(coreTasks.length).fill(false);
            saveAppState();
            startDashboard();
        }

        function startDashboard() {
            showScreen('challenges');
            
            document.getElementById('stat-name').textContent = appState.nickname;
            document.getElementById('stat-avatar').textContent = appState.avatar;
            document.getElementById('stat-phase').textContent = appState.phaseName;
            document.getElementById('stat-score').textContent = appState.score;
            
            let daysLeft = appState.totalDuration - appState.currentDay;
            document.getElementById('days-left').textContent = daysLeft;
            
            let progressPercent = (appState.currentDay / appState.totalDuration) * 100;
            document.getElementById('progress-bar').style.width = progressPercent + '%';

            let listContainer = document.getElementById('challenge-list');
            listContainer.innerHTML = '';

            coreTasks.forEach((taskText, index) => {
                let isChecked = appState.completedTasksOfCurrentDay[index] || false;
                
                let li = document.createElement('li');
                li.className = `task-item ${isChecked ? 'completed-task' : ''}`;
                li.id = `task-box-${index}`;
                
                li.innerHTML = `
                    <div class="task-row">
                        <label for="chk-${index}">${taskText}</label>
                        <input type="checkbox" id="chk-${index}" ${isChecked ? 'checked' : ''} onchange="onTaskToggle(${index})">
                    </div>
                    <div class="proof-section">
                        <label class="proof-btn" for="file-${index}">📸 ارفع صورة الإثبات (اختياري)</label>
                        <input type="file" id="file-${index}" accept="image/*" style="display:none;" onchange="onUploadProof(${index})">
                        <span class="proof-status" id="proof-status-${index}">✔️ تم حفظ الإثبات</span>
                    </div>
                `;
                listContainer.appendChild(li);
            });
        }

        function onTaskToggle(index) {
            let checkbox = document.getElementById(`chk-${index}`);
            let box = document.getElementById(`task-box-${index}`);
            
            appState.completedTasksOfCurrentDay[index] = checkbox.checked;
            
            if (checkbox.checked) {
                box.classList.add('completed-task');
                appState.score += 10;
                popConfetti();
            } else {
                box.classList.remove('completed-task');
                appState.score = Math.max(0, appState.score - 10);
            }
            
            document.getElementById('stat-score').textContent = appState.score;
            saveAppState();
        }

        function onUploadProof(index) {
            let fileInput = document.getElementById(`file-${index}`);
            if (fileInput.files.length > 0) {
                document.getElementById(`proof-status-${index}`).style.display = 'inline-block';
                appState.score += 5;
                document.getElementById('stat-score').textContent = appState.score;
                saveAppState();
            }
        }

        function popConfetti() {
            for (let i = 0; i < 15; i++) {
                let p = document.createElement('div');
                p.className = 'pop';
                p.style.left = Math.random() * 100 + 'vw';
                p.style.background = ['#fff', '#ff3333', 'gold'][Math.floor(Math.random() * 3)];
                p.style.width = Math.random() * 6 + 5 + 'px';
                p.style.height = p.style.width;
                p.style.animationDelay = Math.random() * 0.3 + 's';
                document.body.appendChild(p);
                setTimeout(() => p.remove(), 2000);
            }
        }

        function simulateDayProgress() {
            appState.currentDay++;
            appState.completedTasksOfCurrentDay = new Array(coreTasks.length).fill(false);
            saveAppState();

            let daysLeft = appState.totalDuration - appState.currentDay;
            if (daysLeft <= 0) {
                showScreen('final');
            } else {
                startDashboard();
                window.scrollTo({ top: 0, behavior: 'smooth' });
            }
        }

        // 📱 ميزة ربط التقرير بالواتساب الشخصي للكابتن فوراً لتلقي النتائج والمبيعات
        function sendReportToWhatsApp() {
            let note = document.getElementById('return-sentence').value.trim();
            if(note === '') { alert('اكتب أي كلمة أو نتيجة عشان تبعتها للكابتن!'); return; }
            
            localStorage.setItem('vanish360_final_note', note);

            // استبدل الرقم اللي تحت برقم واتسابك الحقيقي (اكتبه بالصيغة الدولية بدون أصفار أو علامة +)
            // مثلاً لو رقمك مصري: 201012345678
            let myWhatsAppNumber = "201000000000"; 
            
            let messageText = `🔥 تقرير إتمام تحدي RIDZR 360° 🔥\n\n👤 اللاعب: ${appState.nickname} ${appState.avatar}\n🎯 المرحلة: ${appState.phaseName}\n🏆 مجموع النقاط الحاصل عليها: ${appState.score} نقطة\n\n📝 تقرير التحول والنتائج:\n${note}`;
            
            let encodedMessage = encodeURIComponent(messageText);
            let whatsappUrl = `https://wa.me/${myWhatsAppNumber}?text=${encodedMessage}`;
            
            // فتح شات الواتساب تلقائياً بالرسالة الجاهزة
            window.open(whatsappUrl, '_blank');
            
            showScreen('after');
        }

        function saveAppState() { localStorage.setItem('vanish360_premium_state', JSON.stringify(appState)); }

        function resetPlatformData() {
            if (confirm('هل أنت متأكد من مسح البيانات والبدء مجدداً؟')) {
                let backupUnlocked = appState.isPaidUnlocked;
                localStorage.removeItem('vanish360_premium_state');
                localStorage.removeItem('vanish360_final_note');
                appState = { isPaidUnlocked: backupUnlocked, nickname: '', avatar: '🥷', phaseName: '', totalDuration: 10, currentDay: 0, score: 0, completedTasksOfCurrentDay: [] };
                document.getElementById('user-nickname').value = '';
                showScreen('arrival');
            }
        }

        window.addEventListener('DOMContentLoaded', () => {
            let saved = localStorage.getItem('vanish360_premium_state');
            if (saved) {
                appState = JSON.parse(saved);
                if (!appState.isPaidUnlocked) { showScreen('paywall'); } 
                else if (appState.nickname === '') { showScreen('identity'); } 
                else if (appState.phaseName === '') { showScreen('phases'); } 
                else if (appState.currentDay >= appState.totalDuration) { showScreen('final'); } 
                else { startDashboard(); }
            }
        });
    </script>
</body>
</html>
