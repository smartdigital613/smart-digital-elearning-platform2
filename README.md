<!-- USER PANEL (TEAL THEME + FIXED LONG NOTICE SYSTEM) -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Digital E-Learning Platform</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        /* TEAL THEME VARIABLES */
        :root { 
            --primary: #00695c; 
            --bg-gradient: linear-gradient(to bottom, #004d40, #00695c); 
            --card-bg: rgba(255, 255, 255, 0.95);
            --text-main: #ffffff; 
            --text-dark: #333333; 
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        
        body { 
            background: var(--bg-gradient); 
            background-attachment: fixed; 
            color: var(--text-main); 
            height: 100vh; 
            overflow-x: hidden; 
        }
        
        .screen { display: none; padding-bottom: 100px; min-height: 100vh; }
        .active-screen { display: block; animation: fadeIn 0.4s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        /* CARD STYLES */
        .header { 
            background: var(--card-bg); 
            color: var(--text-dark);
            padding: 15px 20px; 
            display: flex; justify-content: space-between; align-items: center; 
            box-shadow: 0 4px 10px rgba(0,0,0,0.2); 
            position: sticky; top: 0; z-index: 100; 
            border-bottom: 1px solid rgba(0,0,0,0.1);
        }

        .auth-container, .mj-card, .history-card, .sa-card, .copy-box, .section-box { 
            background: var(--card-bg); 
            color: var(--text-dark);
            padding: 20px; 
            border-radius: 12px; 
            box-shadow: 0 4px 6px rgba(0,0,0,0.1); 
            margin-bottom: 15px;
        }

        .auth-container { max-width: 400px; margin: 40px auto; text-align: center; }

        .logo { font-size: 18px; font-weight: bold; color: var(--primary); margin-bottom: 20px; }
        input, select { 
            width: 100%; padding: 12px; margin: 8px 0; 
            border: 1px solid #ccc; border-radius: 8px; 
            background: #f9f9f9; color: #333; 
        }
        
        button { 
            width: 100%; padding: 12px; 
            background: var(--primary); color: white; 
            border: none; border-radius: 8px; 
            font-weight: 600; cursor: pointer; margin-top: 15px; 
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
            transition: 0.2s;
        }
        button:active { transform: scale(0.98); }
        .link { margin-top: 15px; font-size: 13px; color: var(--primary); cursor: pointer; text-decoration: underline; }

        /* --- UPDATED NOTICE BOARD STYLE --- */
        .notice-wrapper {
            background: #fff3cd;
            border-radius: 8px;
            margin: 15px;
            padding: 5px;
            display: flex;
            align-items: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .notice-icon {
            background: #ffecb3;
            padding: 8px;
            border-radius: 5px;
            font-size: 20px;
            margin-right: 5px;
        }
        .notice-content {
            flex: 1;
            overflow: hidden;
            color: #856404;
            font-weight: 500;
        }
        .notice-btn {
            background: #ffc107;
            color: #333;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 11px;
            cursor: pointer;
            margin-top: 0;
            width: auto;
            white-space: nowrap;
        }
        /* --- END NOTICE STYLE --- */
        
        .login-tabs { display: flex; justify-content: center; margin-bottom: 20px; background: #eee; border-radius: 25px; padding: 5px; }
        .tab { flex: 1; padding: 10px; cursor: pointer; border-radius: 20px; font-size: 14px; font-weight: 600; text-align: center; transition: 0.3s; color: #555; }
        .tab.active-std { background: var(--primary); color: white; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        .tab.active-sub { background: #004d40; color: white; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        
        .mj-tabs { display: flex; gap: 10px; margin-bottom: 10px; overflow-x: auto; }
        .mj-tab { padding: 8px 15px; background: rgba(255,255,255,0.8); color: #333; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; white-space: nowrap; }
        .mj-tab.active { background: var(--primary); color: white; }

        .mj-card { display: flex; align-items: center; gap: 15px; padding: 15px; cursor: pointer; position: relative; }
        .mj-card.disabled { opacity: 0.6; pointer-events: none; background: #e0e0e0; }
        .mj-img { width: 60px; height: 60px; border-radius: 8px; object-fit: cover; background: #eee; flex-shrink: 0; }
        .mj-info { flex: 1; }
        .mj-price { background: #e0f2f1; color: #00695c; padding: 3px 8px; border-radius: 5px; font-weight: bold; font-size: 12px; }
        
        .sidebar { position: fixed; top: 0; left: -280px; width: 280px; height: 100%; background: white; color: #333; transition: 0.3s; z-index: 200; padding: 20px; box-shadow: 2px 0 10px rgba(0,0,0,0.2); }
        .sidebar.open { left: 0; }
        .menu-item { padding: 15px; border-bottom: 1px solid #eee; cursor: pointer; color: #333; }
        .overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: none; z-index: 150; }
        .menu-btn { font-size: 24px; cursor: pointer; color: #333; }

        .btn-highlight { 
            background: linear-gradient(45deg, #ff6f00, #ffca28); 
            color: white; 
            font-size: 16px; 
            padding: 15px; 
            border-radius: 10px; 
            box-shadow: 0 4px 10px rgba(255, 111, 0, 0.3);
            margin-bottom: 15px;
            animation: pulse 2s infinite;
        }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.02); } 100% { transform: scale(1); } }
        
        .pay-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 15px 0; }
        .pay-option { border: 1px solid #ddd; border-radius: 8px; padding: 10px; text-align: center; cursor: pointer; background: white; }
        .pay-option.selected { border: 2px solid var(--primary); background: #e0f2f1; }
        .pay-logo { width: 50px; height: 50px; object-fit: contain; }
        
        .notice-img { width: 100%; max-height: 200px; object-fit: cover; margin: 10px 0; border-radius: 8px; display: block; }
        
        .status-badge { float: right; padding: 2px 8px; border-radius: 4px; color: white; font-size: 10px; font-weight: bold; }
        .sa-bal { background: linear-gradient(135deg, #00897b 0%, #004d40 100%); color: white; padding: 25px; border-radius: 15px; text-align: center; margin-bottom: 20px; }
    </style>
</head>
<body>

    <!-- LOGIN SCREEN -->
    <div id="loginScreen" class="screen active-screen">
        <div class="auth-container">
            <h3>Smart Digital E-Learning</h3>
            <div class="login-tabs">
                <div id="tabStd" class="tab active-std" onclick="switchLogin('student')">👨‍🎓 Student</div>
                <div id="tabSub" class="tab" onclick="switchLogin('staff')">💼 Official</div>
            </div>
            
            <div id="studentForm">
                <input type="number" id="stdPhone" placeholder="Student Phone Number">
                <input type="password" id="stdPass" placeholder="Password">
                <button onclick="handleStudentLogin()">Login as Student</button>
                <div class="link" onclick="showScreen('registerScreen')">Create New Student Account</div>
            </div>

            <div id="subAdminForm" style="display:none;">
                <label>Select Your Rank:</label>
                <select id="staffRole">
                    <option value="staff_trainers">Trainer</option>
                    <option value="staff_leaders">Team Leader</option>
                    <option value="staff_seniors">Senior Team Leader</option>
                    <option value="staff_teachers">Teacher</option>
                </select>
                <input type="number" id="saPhone" placeholder="Phone Number">
                <input type="password" id="saPass" placeholder="Password">
                <button onclick="handleStaffLogin()" style="background:#004d40;">Login</button>
            </div>
            
            <div style="margin-top:20px;"><button onclick="window.open('https://wa.me/8801992539017')" style="background:#25D366;">WhatsApp Help</button></div>
        </div>
    </div>

    <!-- REGISTER SCREEN -->
    <div id="registerScreen" class="screen">
        <div class="auth-container">
            <div class="logo">📝 Student Registration</div>
            <input type="text" id="regName" placeholder="Full Name">
            <input type="email" id="regEmail" placeholder="Gmail">
            <input type="number" id="regPhone" placeholder="WhatsApp Number">
            <input type="password" id="regPass" placeholder="Password">
            <input type="text" id="regRef" placeholder="Referral Code (Optional)">
            <button onclick="handleRegister()">Register Now</button>
            <div class="link" onclick="showScreen('loginScreen')">Back to Login</div>
        </div>
    </div>

    <!-- VERIFICATION SCREEN -->
    <div id="verifyScreen" class="screen">
        <div class="header"><div class="logo" style="margin:0 auto; color: orange;">⚠️ Verification</div></div>
        <div class="auth-container">
            <h3>Pay <span id="verFee" style="color:red;">৳50</span></h3>
            <div style="background:#eee; padding:10px; border-radius:5px; margin:10px 0; font-weight:bold; color:#333;">01402014519</div>
            <label>Select Payment Method:</label>
            <div class="pay-grid">
                <div class="pay-option" onclick="selectVerifyMethod('Bkash', this)"><img src="https://i.ibb.co/M7yj5y1/bkash.png" class="pay-logo logo-bkash"><br>Bkash</div>
                <div class="pay-option" onclick="selectVerifyMethod('Nagad', this)"><img src="https://i.ibb.co/2N5qg93/nagad.png" class="pay-logo logo-nagad"><br>Nagad</div>
                <div class="pay-option" onclick="selectVerifyMethod('Rocket', this)"><img src="https://i.ibb.co/Wc6152x/rocket.png" class="pay-logo logo-rocket"><br>Rocket</div>
            </div>
            <input type="hidden" id="verifyMethod">
            <input type="text" id="verifyTrx" placeholder="Enter TrxID here...">
            <button onclick="submitVerification()">Submit Verification</button>
            <div style="margin-top: 10px; text-align:center;"><span onclick="logout()" style="color:red; cursor:pointer;">Logout</span></div>
        </div>
    </div>

    <!-- STUDENT DASHBOARD -->
    <div id="dashboardScreen" class="screen">
        <div class="header">
            <div class="menu-btn" onclick="toggleMenu()">☰</div>
            <div class="logo" style="font-size: 14px;">Smart E-Learning</div>
            <div onclick="showProfile()">👤</div>
        </div>
        
        <!-- UPDATED NOTICE BOARD WITH VIEW BUTTON -->
        <div class="notice-wrapper">
            <div class="notice-icon">📢</div>
            <div class="notice-content">
                <marquee id="noticeMarquee" scrollamount="4">Welcome...</marquee>
            </div>
            <button class="notice-btn" onclick="openNoticeModal()">View Full</button>
        </div>
        
        <div style="padding:0 15px;">
            <img id="dashNoticeImg" class="notice-img" src="" style="display:none;">
            
            <!-- OFFICIAL APP BUTTON -->
            <button onclick="showScreen('appScreen')" class="btn-highlight">
                📱 Download Official App
            </button>
            
            <button onclick="showScreen('gmailScreen')" style="background:#db4437; margin-bottom:10px;">📧 Sell Gmail (Income)</button>
            <button onclick="joinLive()" style="background:red;">🔴 Join Live Class</button>
        </div>

        <div style="padding:0 20px; margin-top:20px;">
            <h3 style="color:white; margin-bottom:10px;">Courses</h3>
            <div id="classList">Loading...</div>
        </div>
        
        <div style="position:fixed; bottom:20px; right:20px; background:#25D366; width:50px; height:50px; border-radius:50%; display:flex; align-items:center; justify-content:center; color:white; font-size:24px; cursor:pointer; z-index:999; box-shadow:0 4px 10px rgba(0,0,0,0.3);" onclick="window.open('https://wa.me/8801992539017')">📞</div>
    </div>

    <!-- NOTICE MODAL (FOR LONG NOTICES) -->
    <div id="noticeModal" class="screen" style="z-index: 500;">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Notice Board</div>
        <div style="padding:20px;">
            <div class="mj-card" style="display:block;">
                <h3 style="color:#00695c; border-bottom:1px solid #eee; padding-bottom:10px; margin-bottom:10px;">📢 Full Notice</h3>
                <p id="fullNoticeText" style="white-space: pre-wrap; line-height: 1.6;">Loading...</p>
            </div>
        </div>
    </div>

    <!-- SIDEBAR -->
    <div class="overlay" id="overlay" onclick="toggleMenu()"></div>
    <div class="sidebar" id="sidebar">
        <div style="text-align:right; font-size:20px; cursor:pointer;" onclick="toggleMenu()">✖</div>
        <h3>Menu</h3><hr>
        <div id="menuItems"></div>
        <div class="menu-item" onclick="logout()" style="color:red;">🚪 Logout</div>
    </div>

    <!-- ALL HISTORY SCREEN -->
    <div id="allHistoryScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> All History</div>
        <div style="padding:15px;">
            <div class="section-box"><h4 style="color:#004d40;">💰 Withdrawals</h4><div id="hWithdraw">No history found.</div></div>
            <div class="section-box"><h4 style="color:#004d40;">📧 Gmail Tasks</h4><div id="hGmail">No history found.</div></div>
            <div class="section-box"><h4 style="color:#004d40;">📝 Job Proofs</h4><div id="hTask">No history found.</div></div>
        </div>
    </div>

    <!-- GMAIL SCREEN -->
    <div id="gmailScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Sell Gmail</div>
        <div class="auth-container">
            <h3>📧 Submit Gmail</h3>
            <div class="copy-box">
                <p style="font-size:12px; color:red; margin-bottom:5px;">⚠️ নিচের মেইল এবং পাসওয়ার্ড দিয়ে নতুন জিমেইল খুলুন:</p>
                <div class="copy-row">
                    <span>Rec. Mail: <b id="showRecEmail">Loading...</b></span>
                    <button onclick="copyToClip(document.getElementById('showRecEmail').innerText)" style="padding:2px 8px; width:auto; font-size:10px; margin:0;">Copy</button>
                </div>
                <div class="copy-row">
                    <span>Password: <b id="showDefPass">Loading...</b></span>
                    <button onclick="copyToClip(document.getElementById('showDefPass').innerText)" style="padding:2px 8px; width:auto; font-size:10px; margin:0;">Copy</button>
                </div>
            </div>

            <input type="email" id="jobGmail" placeholder="New Gmail Address">
            <input type="text" id="jobGPass" placeholder="Password (Use the one above)">
            <button onclick="submitGmailJob()">Submit Account</button>
        </div>
    </div>

    <!-- PROFILE SCREEN -->
    <div id="profileScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Profile</div>
        <div class="auth-container">
            <h2 id="pName">Name</h2>
            <p id="pID" style="color:blue; font-weight:bold;">ID: ...</p>
            <p id="pPhone">Phone</p>
            <button onclick="copyRef()">🔗 Copy Referral Link</button>
        </div>
    </div>

    <!-- WITHDRAW SCREEN (Student) -->
    <div id="withdrawScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Wallet</div>
        <div class="auth-container" style="background:#00695c; color:white;"><h1>৳ <span id="bal">0</span></h1></div>
        <div style="padding:20px;">
            <div class="section-box">
                <label style="font-weight:bold;">Select Withdraw Method:</label>
                <div class="pay-grid">
                    <div class="pay-option" onclick="selectWithdrawMethod('Bkash', this)"><img src="https://i.ibb.co/M7yj5y1/bkash.png" class="pay-logo logo-bkash"><br>Bkash</div>
                    <div class="pay-option" onclick="selectWithdrawMethod('Nagad', this)"><img src="https://i.ibb.co/2N5qg93/nagad.png" class="pay-logo logo-nagad"><br>Nagad</div>
                    <div class="pay-option" onclick="selectWithdrawMethod('Rocket', this)"><img src="https://i.ibb.co/Wc6152x/rocket.png" class="pay-logo logo-rocket"><br>Rocket</div>
                </div>
                <input type="hidden" id="wMethod">
                <input id="wAmount" type="number" placeholder="Amount">
                <input id="wNumber" type="number" placeholder="Wallet Number">
                <button onclick="handleWithdraw()">Withdraw</button>
            </div>
        </div>
    </div>

    <!-- DEDICATED APP SCREEN -->
    <div id="appScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Official Apps</div>
        <div style="padding:15px;">
            <div class="notice-board" style="text-align:center;">
                <h3 style="color:#e65100;">📲 Download Apps</h3>
                <small>Download official apps safely from here.</small>
            </div>
            <div id="onlyAppList">Loading Apps...</div>
        </div>
    </div>

    <!-- TASK SCREEN -->
    <div id="taskScreen" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('dashboardScreen')">⬅</button> Tasks & Jobs</div>
        <div style="padding:15px;">
            <div class="mj-tabs">
                <div class="mj-tab active" onclick="switchMjTab('tabJobs', this)">Microjobs</div>
                <div class="mj-tab" onclick="switchMjTab('tabFiles', this)">Downloads</div>
            </div>

            <div id="tabJobs" class="mj-content active">
                <div id="microjobList" style="padding-bottom:50px;">Loading...</div>
                <hr style="border-color: rgba(255,255,255,0.2); margin: 20px 0;">
                <h4 style="color:white; margin-bottom:10px;">Simple Link Jobs</h4>
                <div id="jobList">Loading...</div>
            </div>

            <div id="tabFiles" class="mj-content">
                <div id="downloadList">Loading...</div>
            </div>
        </div>
    </div>

    <!-- JOB DETAILS MODAL -->
    <div id="mjModal" class="screen" style="z-index:500;">
        <div class="header"><button class="menu-btn" onclick="showScreen('taskScreen')">⬅</button> Job Details</div>
        <div style="padding:20px;">
            <div class="mj-card" style="display:block;">
                <h2 id="mjdTitle">Title</h2>
                <span id="mjdPrice" class="mj-price" style="font-size:16px;">৳0</span>
                <img id="mjdImg" src="" style="width:100%; border-radius:10px; margin:10px 0; display:none;">
                <p id="mjdDesc" style="margin:10px 0; background:#f9f9f9; padding:10px; border-radius:5px; white-space: pre-wrap; color:#333;"></p>
                <a id="mjdLink" href="#" target="_blank" class="btn" style="display:block; text-align:center; background:#00695c; text-decoration:none; color:white; padding:10px; border-radius:5px; margin-bottom:15px;">Open Job Link 🔗</a>
                <label>Submit Proof:</label>
                <input id="mjdProof" placeholder="Paste Screenshot/Proof Link here...">
                <input type="hidden" id="mjdId">
                <input type="hidden" id="mjdReward">
                <button onclick="submitDetailedTask()">Submit Job</button>
            </div>
        </div>
    </div>

    <!-- STAFF DASHBOARD -->
    <div id="staffDash" class="screen">
        <div class="header">
            <div class="menu-btn" onclick="toggleMenu()">☰</div>
            <div class="logo" style="font-size: 14px; color:#00897b;">Official Panel</div>
            <div onclick="logout()">🚪</div>
        </div>
        <div style="padding:20px;">
            <div class="sa-bal"><small>Income Balance</small><h1 id="saBal">৳ 0</h1></div>
            <div id="roleBadge" style="text-align:center; padding:5px; background:#e0f2f1; border-radius:5px; margin-bottom:15px; color:#00897b; font-weight:bold;">...</div>
            
            <h4 style="color:white;">👥 Assigned Students</h4>
            <input id="saSearch" placeholder="Search ID/Phone..." onkeyup="filterMyStudents()">
            <div id="myStudentList">Loading...</div>
        </div>
    </div>

    <!-- STAFF PROFILE -->
    <div id="staffProfile" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('staffDash')">⬅</button><div>My Profile</div></div>
        <div class="auth-container">
            <div style="font-size:50px;">👔</div>
            <h2 id="saPName">Name</h2>
            <p id="saPPhone" style="color:gray;">Phone</p>
        </div>
    </div>

    <!-- STAFF WALLET -->
    <div id="staffWallet" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('staffDash')">⬅</button><div>Withdraw Income</div></div>
        <div class="auth-container">
            <div class="sa-bal" style="margin-bottom:10px;"><h1 id="saWBal">৳ 0</h1></div>
            <label style="font-weight:bold;">Select Withdraw Method:</label>
            <div class="pay-grid">
                <div class="pay-option" onclick="selectSaWithdrawMethod('Bkash', this)"><img src="https://i.ibb.co/M7yj5y1/bkash.png" class="pay-logo logo-bkash"><br>Bkash</div>
                <div class="pay-option" onclick="selectSaWithdrawMethod('Nagad', this)"><img src="https://i.ibb.co/2N5qg93/nagad.png" class="pay-logo logo-nagad"><br>Nagad</div>
                <div class="pay-option" onclick="selectSaWithdrawMethod('Rocket', this)"><img src="https://i.ibb.co/Wc6152x/rocket.png" class="pay-logo logo-rocket"><br>Rocket</div>
            </div>
            <input type="hidden" id="saWMethod">
            <input id="saWAmount" type="number" placeholder="Amount (Min 50)">
            <input id="saWNumber" type="number" placeholder="Wallet Number">
            <button onclick="staffWithdrawRequest()" style="background:#00695c;">Withdraw Request</button>
        </div>
    </div>

    <!-- STAFF HISTORY -->
    <div id="staffHistory" class="screen">
        <div class="header"><button class="menu-btn" onclick="showScreen('staffDash')">⬅</button><div>My History</div></div>
        <div style="padding:15px;">
            <h4 style="color:white;">Withdrawal History</h4>
            <div id="saWHistory">Loading...</div>
        </div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyBNquB4k0OCS0eFWJKuo6GlW4fAcfWhHaI", authDomain: "smart-learning-platform-e3f0b.firebaseapp.com", projectId: "smart-learning-platform-e3f0b", databaseURL: "https://smart-learning-platform-e3f0b-default-rtdb.firebaseio.com" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();
        
        let currentUser = null;
        let currentRoleTable = "";
        let currentRoleName = "";
        let myAssignedIDs = [];
        let allMyStudents = [];
        let allMicrojobs = {}; 
        let allTasks = []; 

        const urlParams = new URLSearchParams(window.location.search);
        if(urlParams.get('ref')) { document.getElementById('regRef').value = urlParams.get('ref'); showScreen('registerScreen'); }

        function selectPay(method, el, inputId) {
            document.querySelectorAll('.pay-option').forEach(e => e.classList.remove('selected'));
            el.classList.add('selected'); document.getElementById(inputId).value = method;
        }
        window.selectVerifyMethod = function(m, el) { selectPay(m, el, 'verifyMethod'); }
        window.selectWithdrawMethod = function(m, el) { selectPay(m, el, 'wMethod'); }
        window.selectSaWithdrawMethod = function(m, el) { selectPay(m, el, 'saWMethod'); }

        window.switchLogin = function(type) {
            if(type === 'student') {
                document.getElementById('studentForm').style.display = 'block'; document.getElementById('subAdminForm').style.display = 'none'; document.getElementById('tabStd').classList.add('active-std'); document.getElementById('tabSub').classList.remove('active-sub');
            } else {
                document.getElementById('studentForm').style.display = 'none'; document.getElementById('subAdminForm').style.display = 'block'; document.getElementById('tabStd').classList.remove('active-std'); document.getElementById('tabSub').classList.add('active-sub');
            }
        }

        window.handleStudentLogin = function() {
            const phone = document.getElementById('stdPhone').value;
            const pass = document.getElementById('stdPass').value;
            db.ref('users').orderByChild('phone').equalTo(phone).once('value', snap => {
                let found = false;
                if(snap.exists()) {
                    snap.forEach(c => {
                        if(c.val().password == pass) {
                            found = true;
                            if(c.val().isBlocked) return alert("Account Blocked!");
                            currentUser = {...c.val(), key: c.key};
                            initStudent();
                        }
                    });
                }
                if(!found) alert("Invalid Student Credentials");
            });
        }

        window.handleStaffLogin = function() {
            const roleTable = document.getElementById('staffRole').value;
            const phone = document.getElementById('saPhone').value;
            const pass = document.getElementById('saPass').value;
            
            db.ref(roleTable).orderByChild('phone').equalTo(phone).once('value', snap => {
                let found = false;
                if(snap.exists()) {
                    snap.forEach(c => {
                        if(c.val().password == pass) {
                            found = true;
                            if(c.val().isBlocked) return alert("Account Blocked!");
                            currentUser = {...c.val(), key: c.key};
                            currentRoleTable = roleTable;
                            currentRoleName = document.getElementById('staffRole').options[document.getElementById('staffRole').selectedIndex].text;
                            initStaff();
                        }
                    });
                }
                if(!found) alert("Invalid Credentials or Wrong Role Selected");
            });
        }

        function initStudent() {
            setupMenu('student');
            db.ref('settings').once('value', settingSnap => {
                const settings = settingSnap.val();
                const isVerifyOn = (settings && settings.verificationActive === false) ? false : true;
                if (currentUser.isVerified) showScreen('dashboardScreen');
                else {
                    if (isVerifyOn === false) {
                        db.ref('users/' + currentUser.key).update({ isVerified: true });
                        currentUser.isVerified = true; alert("Verification is OFF. Auto Verified! 🎉"); showScreen('dashboardScreen');
                    } else showScreen('verifyScreen');
                }
            });

            db.ref('users/'+currentUser.key).on('value', s => { if(!s.exists()) return; currentUser = {...s.val(), key: currentUser.key}; document.getElementById('bal').innerText = currentUser.balance; document.getElementById('pName').innerText = currentUser.name; document.getElementById('pID').innerText = "ID: " + currentUser.id; document.getElementById('pPhone').innerText = currentUser.phone; });
            
            // Gmail History Viewer
            db.ref('gmail_tasks').orderByChild('studentId').equalTo(currentUser.id).on('value', s => { 
                const d = document.getElementById('hGmail'); d.innerHTML=""; let arr = []; s.forEach(c => arr.push(c.val())); 
                if(arr.length === 0) d.innerHTML = "No Gmail history."; 
                arr.reverse().forEach(g => { 
                    let color = g.status=='Approved'?'green':(g.status=='Rejected'?'red':'orange'); 
                    d.innerHTML += `<div class="history-card" style="border-left-color:${color}">
                        Gmail: <b>${g.gmail}</b> <button onclick="copyToClip('${g.gmail}')" style="padding:2px; font-size:10px; width:auto;">Copy</button><br>
                        Pass: <b>${g.password}</b> <button onclick="copyToClip('${g.password}')" style="padding:2px; font-size:10px; width:auto;">Copy</button><br>
                        <span class="status-badge" style="background:${color}">${g.status}</span>
                    </div>`; 
                }); 
            });

            db.ref('withdrawals').orderByChild('studentId').equalTo(currentUser.id).on('value', s => { const d = document.getElementById('hWithdraw'); d.innerHTML=""; let arr=[]; s.forEach(c=>arr.push(c.val())); if(arr.length === 0) d.innerHTML = "No Withdrawal history."; arr.reverse().forEach(w => { let color = w.status=='Approved'?'green':(w.status=='Rejected'?'red':'orange'); d.innerHTML += `<div class="history-card" style="border-left-color:${color}">৳${w.amount} (${w.method||'Bkash'}) - <span class="status-badge" style="background:${color}">${w.status}</span></div>`; }); });
            
            // Task History
            db.ref('tasks').on('value', s => { 
                const d = document.getElementById('hTask'); d.innerHTML=""; 
                allTasks = []; // Global tasks list
                s.forEach(c => allTasks.push(c.val()));
                
                const myTasks = allTasks.filter(t => t.studentId === currentUser.id);
                if(myTasks.length === 0) d.innerHTML = "No Job history."; 
                myTasks.slice().reverse().forEach(t => { 
                    let color = t.status=='Approved'?'green':(t.status=='Rejected'?'red':'orange'); 
                    d.innerHTML += `<div class="history-card" style="border-left-color:${color}">
                        <b>${t.mjTitle || 'Job'}</b><br>
                        Status: <span class="status-badge" style="background:${color}">${t.status}</span>
                        ${t.status === 'Rejected' && t.rejectionReason ? `<br><small style="color:red">Reason: ${t.rejectionReason}</small>` : ''}
                    </div>`; 
                });
                renderJobs(); // Refresh jobs status
            });
            
            db.ref('admin_classes').on('value', snap => { const list = document.getElementById('classList'); list.innerHTML = ""; if(snap.exists()) snap.forEach(c => { const val = c.val(); list.innerHTML += `<div class="mj-card"><div style="display:flex; gap:10px;"><img src="${val.img}" style="width:60px; height:60px; object-fit:cover; border-radius:5px;"><div><b>${val.title}</b><br><small>${val.desc}</small></div></div></div>`; }); else list.innerHTML = "<p style='color:white'>No classes available.</p>"; });
            db.ref('admin_jobs').on('value', snap => { document.getElementById('jobList').innerHTML = ""; snap.forEach(c => document.getElementById('jobList').innerHTML += `<div class="history-card" style="border-left-color:blue;"><b>${c.val().title}</b> (৳${c.val().reward})<br><a href="${c.val().link}" target="_blank">Start Work</a></div>`); });
            
            // Listeners for Microjobs
            db.ref('microjobs').on('value', snap => {
                allMicrojobs = {}; 
                if(snap.exists()) snap.forEach(c => allMicrojobs[c.key] = c.val());
                renderJobs();
            });

            // Listeners for Files
            db.ref('download_files').on('value', snap => {
                const list = document.getElementById('downloadList'); list.innerHTML = "";
                if(snap.exists()) snap.forEach(c => {
                    const f = c.val();
                    list.innerHTML += `<div class="mj-card">
                        <div class="mj-info"><b>${f.title}</b><br><span class="mj-price">Value: ৳${f.reward}</span></div>
                        <a href="${f.link}" target="_blank" class="btn btn-green" style="width:auto; padding:5px 10px; text-decoration:none; background:#00695c; color:white;">Download</a>
                    </div>`;
                });
            });

            // LISTENERS FOR APPS (FOR DEDICATED PAGE)
            db.ref('apps').on('value', snap => {
                const list = document.getElementById('onlyAppList'); list.innerHTML = "";
                if(snap.exists()) snap.forEach(c => {
                    const a = c.val();
                    list.innerHTML += `<div class="mj-card" style="border: 2px solid #e65100;">
                        ${a.icon ? `<img src="${a.icon}" class="mj-img">` : '<div class="mj-img" style="display:flex;align-items:center;justify-content:center;font-size:24px;">📱</div>'}
                        <div class="mj-info">
                            <b>${a.title}</b><br>
                            <span style="font-size:11px; color:gray;">${a.desc || 'No description'}</span>
                        </div>
                        <a href="${a.link}" target="_blank" class="btn btn-green" style="width:auto; padding:5px 10px; text-decoration:none; background:#e65100; color:white;">Download</a>
                    </div>`;
                });
            });

            db.ref('settings').on('value', snap => {
                if(snap.val()) {
                    const s = snap.val(); 
                    // Update Notice in Marquee and Modal
                    const noticeText = s.notice || "Welcome";
                    document.getElementById('noticeMarquee').innerText = noticeText;
                    document.getElementById('fullNoticeText').innerText = noticeText;
                    
                    document.getElementById('verFee').innerText = "৳" + (s.fee||50);
                    document.getElementById('showRecEmail').innerText = s.recoveryEmail || "Not Set";
                    document.getElementById('showDefPass').innerText = s.defaultPass || "Not Set";
                    if(s.noticeImg) { document.getElementById('dashNoticeImg').src = s.noticeImg; document.getElementById('dashNoticeImg').style.display='block'; } else document.getElementById('dashNoticeImg').style.display='none';
                    if(s.paymentLogos) { if(s.paymentLogos.bkash) document.querySelectorAll('.logo-bkash').forEach(i => i.src = s.paymentLogos.bkash); if(s.paymentLogos.nagad) document.querySelectorAll('.logo-nagad').forEach(i => i.src = s.paymentLogos.nagad); if(s.paymentLogos.rocket) document.querySelectorAll('.logo-rocket').forEach(i => i.src = s.paymentLogos.rocket); }
                }
            });
        }
        
        // --- FIXED RENDER FUNCTION (DOM APPENDING) ---
        function renderJobs() {
            const list = document.getElementById('microjobList'); 
            list.innerHTML = ""; // Clear existing

            const keys = Object.keys(allMicrojobs);
            if (keys.length === 0) {
                list.innerHTML = "<p style='text-align:center; color:white;'>No active jobs available.</p>";
                return;
            }

            keys.reverse().forEach(key => {
                const j = allMicrojobs[key];
                const submittedCount = allTasks.filter(t => t.jobId === key).length;
                const isFull = j.limit && submittedCount >= j.limit;
                const iDidIt = allTasks.some(t => t.jobId === key && t.studentId === currentUser.id);
                
                const div = document.createElement('div');
                div.className = 'mj-card';
                if (iDidIt || isFull) div.classList.add('disabled');
                
                if (!iDidIt && !isFull) {
                    div.onclick = function() { openMjDetails(key); };
                }

                let statusBadge = "";
                if (iDidIt) statusBadge = `<span class="mj-status done">Already Done ✅</span>`;
                else if (isFull) statusBadge = `<span class="mj-status full">Limit Over ❌</span>`;

                div.innerHTML = `
                    <img class="mj-img" src="${j.image || 'https://via.placeholder.com/60'}">
                    <div class="mj-info">
                        <b>${j.title}</b> ${statusBadge}<br>
                        <span class="mj-price">Earn ৳${j.reward}</span> 
                        <span style="font-size:10px; color:grey;">(${submittedCount}/${j.limit || '∞'})</span>
                    </div>
                    <div style="font-size:20px;">➔</div>
                `;

                list.appendChild(div);
            });
        }
        
        // OPEN NOTICE MODAL
        window.openNoticeModal = function() {
            showScreen('noticeModal');
        }

        function initStaff() {
            setupMenu('staff'); showScreen('staffDash');
            db.ref(`${currentRoleTable}/${currentUser.key}`).on('value', s => {
                if(!s.exists()) return;
                currentUser = {...s.val(), key: currentUser.key};
                document.getElementById('saBal').innerText = "৳ " + (currentUser.balance || 0);
                document.getElementById('saWBal').innerText = "৳ " + (currentUser.balance || 0);
                document.getElementById('saPName').innerText = currentUser.name;
                document.getElementById('saPPhone').innerText = currentUser.phone;
                document.getElementById('roleBadge').innerText = "Role: " + currentRoleName;
                if(currentUser.assigned) myAssignedIDs = Object.values(currentUser.assigned);
                loadMyStudents();
            });
            db.ref('staff_withdrawals').orderByChild('userKey').equalTo(currentUser.key).on('value', s => {
                const d = document.getElementById('saWHistory'); d.innerHTML=""; let arr=[]; s.forEach(c=>arr.push(c.val()));
                if(arr.length === 0) d.innerHTML = "No History.";
                arr.reverse().forEach(w => {
                    let color = w.status=='Approved'?'green':(w.status=='Rejected'?'red':'orange');
                    d.innerHTML += `<div class="history-card" style="border-left-color:${color}">৳${w.amount} (${w.method}) <span class="status-badge" style="background:${color}">${w.status}</span><br>To: ${w.number}</div>`;
                });
            });
        }

        function loadMyStudents() {
            db.ref('users').on('value', snap => {
                allMyStudents = [];
                snap.forEach(child => { if(myAssignedIDs.includes(child.val().id)) allMyStudents.push(child.val()); });
                filterMyStudents();
            });
        }
        window.filterMyStudents = function() {
            const q = document.getElementById('saSearch').value.toLowerCase();
            const div = document.getElementById('myStudentList'); div.innerHTML = "";
            const arr = allMyStudents.filter(u => u.id.toLowerCase().includes(q) || u.phone.includes(q));
            if(arr.length === 0) div.innerHTML = "<p>No members found.</p>";
            else arr.forEach(u => div.innerHTML += `<div class="sa-card"><b>${u.name}</b> (${u.id})<br>Phone: ${u.phone} | Bal: ৳${u.balance}</div>`);
        }

        window.staffWithdrawRequest = function() {
            const am = parseInt(document.getElementById('saWAmount').value), met = document.getElementById('saWMethod').value, num = document.getElementById('saWNumber').value;
            if(!met) return alert("Select Payment Method");
            if(currentUser.balance >= am && am >= 50 && num) {
                db.ref(`${currentRoleTable}/${currentUser.key}`).update({balance: currentUser.balance-am});
                db.ref('staff_withdrawals').push({userKey: currentUser.key, roleTable: currentRoleTable, roleTag: currentRoleName, name: currentUser.name, phone: currentUser.phone, amount: am, method: met, number: num, status: 'Pending'});
                alert("Request Sent! Check History.");
            } else alert("Invalid Amount or Balance");
        }

        window.submitGmailJob = function() {
            const mail = document.getElementById('jobGmail').value, pass = document.getElementById('jobGPass').value;
            if(!mail || !pass) return alert("Enter Mail and Password");
            db.ref('gmail_tasks').push({ studentId: currentUser.id, studentName: currentUser.name, gmail: mail, password: pass, status: 'Pending', date: new Date().toLocaleDateString() });
            alert("Submitted!"); document.getElementById('jobGmail').value = ""; document.getElementById('jobGPass').value = "";
        }
        window.handleWithdraw = function() {
            const am = parseInt(document.getElementById('wAmount').value), num = document.getElementById('wNumber').value, met = document.getElementById('wMethod').value;
            if(!met) return alert("Select Payment Method");
            if(currentUser.balance >= am && am >= 100 && num) {
                db.ref('users/'+currentUser.key).update({balance: currentUser.balance-am});
                db.ref('withdrawals').push({studentId:currentUser.id, amount:am, number:num, method:met, status:'Pending'});
                alert("Request Sent!");
            } else alert("Insufficient Balance or Min 100");
        }
        window.handleRegister = function() {
            const name = document.getElementById('regName').value, phone = document.getElementById('regPhone').value, pass = document.getElementById('regPass').value, ref = document.getElementById('regRef').value;
            const id = "STD-" + Math.floor(10000 + Math.random() * 90000);
            if(!name || !phone || !pass) return alert("Fill All Fields");
            db.ref('users').orderByChild('phone').equalTo(phone).once('value', s => {
                if(s.exists()) alert("Phone Exists!");
                else {
                    if(ref) db.ref('users').orderByChild('id').equalTo(ref).once('value', r => { if(r.exists()) { let rKey = Object.keys(r.val())[0]; db.ref('users/'+rKey).update({balance: (r.val()[rKey].balance||0)+10}); } });
                    db.ref('users').push({id, name, phone, password:pass, balance:0, isVerified:false, isBlocked:false}).then(() => { alert("Success! ID: "+id); showScreen('loginScreen'); });
                }
            });
        }
        window.submitVerification = function() {
            const met = document.getElementById('verifyMethod').value; if(!met) return alert("Select Payment Method");
            db.ref('verifications').push({studentId:currentUser.id, studentName:currentUser.name, phone:currentUser.phone, trx:document.getElementById('verifyTrx').value, method:met, userKey: currentUser.key});
            alert("Sent! Wait for Admin."); logout();
        }

        window.switchMjTab = function(id, el) {
            document.querySelectorAll('.mj-content').forEach(d=>d.classList.remove('active'));
            document.querySelectorAll('.mj-tab').forEach(b=>b.classList.remove('active'));
            document.getElementById(id).classList.add('active'); el.classList.add('active');
        }

        window.openMjDetails = function(key) {
            const job = allMicrojobs[key];
            if(!job) return;

            document.getElementById('mjdTitle').innerText = job.title;
            document.getElementById('mjdPrice').innerText = "Earn ৳"+job.reward;
            document.getElementById('mjdDesc').innerText = job.desc; 
            document.getElementById('mjdLink').href = job.link;
            document.getElementById('mjdId').value = key;
            document.getElementById('mjdReward').value = job.reward;
            
            if(job.image) {
                document.getElementById('mjdImg').src = job.image;
                document.getElementById('mjdImg').style.display = 'block';
            } else {
                document.getElementById('mjdImg').style.display = 'none';
            }
            showScreen('mjModal');
        }

        window.submitDetailedTask = function() {
            const proof = document.getElementById('mjdProof').value;
            const jobId = document.getElementById('mjdId').value;
            const rew = document.getElementById('mjdReward').value;
            const title = document.getElementById('mjdTitle').innerText;
            if(!proof) return alert("Proof Link is required!");
            
            db.ref('tasks').push({
                studentId: currentUser.id,
                studentName: currentUser.name,
                jobId: jobId,
                mjTitle: title,
                reward: parseInt(rew),
                proof: proof,
                status: 'Pending',
                date: new Date().toLocaleDateString()
            });
            alert("Task Submitted! Wait for approval.");
            document.getElementById('mjdProof').value = "";
            showScreen('taskScreen');
        }

        window.copyToClip = function(text) {
            navigator.clipboard.writeText(text).then(() => alert("Copied: " + text));
        }

        function setupMenu(role) {
            const m = document.getElementById('menuItems');
            if(role === 'student') m.innerHTML = `<div class="menu-item" onclick="showScreen('dashboardScreen')">🏠 Dashboard</div><div class="menu-item" onclick="showProfile()">👤 Profile</div><div class="menu-item" onclick="showScreen('gmailScreen')">📧 Sell Gmail</div><div class="menu-item" onclick="showScreen('taskScreen')">📝 Task & Microjobs</div><div class="menu-item" onclick="showScreen('withdrawScreen')">💰 Wallet</div><div class="menu-item" onclick="showScreen('allHistoryScreen')">📜 History</div>`;
            else m.innerHTML = `<div class="menu-item" onclick="showScreen('staffDash')">🏠 Dashboard</div><div class="menu-item" onclick="showScreen('staffProfile')">👤 Profile</div><div class="menu-item" onclick="showScreen('staffWallet')">💰 Wallet</div><div class="menu-item" onclick="showScreen('staffHistory')">📜 History</div>`;
        }
        window.joinLive = function() { db.ref('settings/liveLink').once('value', s=>window.open(s.val())); }
        window.copyRef = function() { navigator.clipboard.writeText(location.href.split('?')[0]+"?ref="+currentUser.id); alert("Referral Link Copied!"); }
        window.logout = function() { currentUser=null; currentRoleTable=""; currentRoleName=""; showScreen('loginScreen'); switchLogin('student'); }
        window.showScreen = function(id) { document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active-screen')); document.getElementById(id).classList.add('active-screen'); toggleMenuIfOpen(); }
        window.toggleMenu = function() { document.getElementById('sidebar').classList.toggle('open'); document.getElementById('overlay').style.display = document.getElementById('sidebar').classList.contains('open') ? 'block' : 'none'; }
        function toggleMenuIfOpen() { if(document.getElementById('sidebar').classList.contains('open')) toggleMenu(); }
        window.showProfile = function() { showScreen('profileScreen'); }
    </script>
</body>
</html>
