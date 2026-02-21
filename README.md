<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MADU-SABAR | Prototype</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --madura-red: #B71C1C;   /* Merah tegas khas Madura */
            --madura-black: #212121; /* Hitam Sakera */
            --gold-tengka: #FFD700;  /* Emas untuk poin/gamifikasi */
            --bg-color: #f8f9fa;
        }

        * {
            box-sizing: border-box;
            font-family: 'Poppins', sans-serif;
            margin: 0; padding: 0;
        }

        body {
            background-color: #eceff1;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        /* Tampilan Mobile App (PWA Frame) */
        .app-container {
            width: 100%;
            max-width: 400px;
            background: var(--bg-color);
            min-height: 90vh;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            overflow: hidden;
            position: relative;
        }

        /* Header Nuansa Madura */
        .header {
            background: linear-gradient(135deg, var(--madura-red), var(--madura-black));
            color: white;
            padding: 20px;
            text-align: center;
            border-bottom-left-radius: 20px;
            border-bottom-right-radius: 20px;
            box-shadow: 0 4px 10px rgba(183, 28, 28, 0.3);
        }

        .header h1 { font-size: 22px; font-weight: 700; letter-spacing: 1px; }
        .header p { font-size: 12px; opacity: 0.8; font-style: italic; }

        /* Gamifikasi: Poin Tengka */
        .score-board {
            display: flex;
            justify-content: space-between;
            background: white;
            margin: -20px 20px 20px;
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
            position: relative;
            z-index: 10;
        }

        .score-item { text-align: center; width: 48%; }
        .score-item h3 { font-size: 14px; color: #555; }
        .score-value { font-size: 24px; font-weight: 700; color: var(--gold-tengka); text-shadow: 1px 1px 2px rgba(0,0,0,0.1); }
        .score-value.red { color: var(--madura-red); }

        /* Area Konten Utama */
        .content { padding: 0 20px 20px; }

        .card {
            background: white;
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 15px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.02);
            border-left: 4px solid var(--madura-red);
        }

        h2 { font-size: 16px; margin-bottom: 10px; color: var(--madura-black); }

        input, button { width: 100%; padding: 12px; border-radius: 8px; border: none; font-size: 14px; }
        input { background: #f1f3f5; margin-bottom: 10px; outline: none; border: 1px solid transparent; transition: 0.3s; }
        input:focus { border-color: var(--madura-red); background: white; }
        
        button { cursor: pointer; font-weight: 600; transition: transform 0.1s, opacity 0.3s; }
        button:active { transform: scale(0.98); }
        
        .btn-primary { background: var(--madura-black); color: white; }
        .btn-success { background: #2e7d32; color: white; margin-top: 10px; }

        /* Fitur Interaktif: Fokus & Buddy */
        .task-list { list-style: none; margin-top: 10px; }
        .task-item { background: #f8f9fa; padding: 10px; border-radius: 6px; margin-bottom: 5px; font-size: 13px; display: flex; justify-content: space-between; align-items: center; border-left: 3px solid var(--madura-black);}
        
        .buddy-alert {
            background: #fff3cd; color: #856404;
            padding: 12px; border-radius: 8px; font-size: 12px;
            text-align: center; margin-top: 15px; border: 1px dashed #ffeeba;
            display: none; /* Disembunyikan awalnya */
        }

        /* Animasi Pop-up Notifikasi */
        .toast {
            position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%);
            background: var(--madura-black); color: var(--gold-tengka);
            padding: 10px 20px; border-radius: 20px; font-size: 12px;
            opacity: 0; transition: opacity 0.5s; pointer-events: none;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2); font-weight: bold;
        }
        .toast.show { opacity: 1; }

    </style>
</head>
<body>

<div class="app-container">
    
    <div class="header">
        <h1>MADU-SABAR</h1>
        <p>"Jaga Tengka, Kokohkan Asa"</p>
    </div>

    <div class="score-board">
        <div class="score-item">
            <h3>Poin Tengka</h3>
            <div class="score-value" id="tengkaPoints">100</div>
        </div>
        <div class="score-item">
            <h3>Fokus (Menit)</h3>
            <div class="score-value red" id="focusMinutes">0</div>
        </div>
    </div>

    <div class="content">
        <div class="card">
            <h2>🎯 Apa targetmu hari ini?</h2>
            <input type="text" id="taskInput" placeholder="Contoh: Merangkum Bab 1 Sejarah...">
            <button class="btn-primary" onclick="addTask()">Kunci Target & Beritahu Buddy</button>
            <ul class="task-list" id="taskList"></ul>
        </div>

        <div class="buddy-alert" id="buddyAlert">
            <strong>🔔 Notifikasi Buddy:</strong><br>
            Ksatria (Buddy-mu) baru saja menyelesaikan tugas Fisikanya! Berikan validasi agar Poin Tengka kalian bertambah.
            <button class="btn-success" onclick="validateBuddy()">Validasi Ksatria (+10 Poin)</button>
        </div>
    </div>

    <div class="toast" id="toastMsg">Mantap! Poin Tengka bertambah.</div>

</div>

<script>
    // Inisialisasi Data
    let tengkaPoints = 100;
    let focusMinutes = 0;

    // Fungsi Tambah Tugas
    function addTask() {
        const input = document.getElementById('taskInput');
        const taskText = input.value.trim();
        
        if(taskText === "") {
            showToast("Tulis targetmu dulu, Rek!");
            return;
        }

        // Tambah ke daftar (UI)
        const ul = document.getElementById('taskList');
        const li = document.createElement('li');
        li.className = 'task-item';
        li.innerHTML = `<span>${taskText}</span> <span style="font-size:10px; color:gray;">Sedang dikerjakan...</span>`;
        ul.appendChild(li);
        
        // Bersihkan input
        input.value = "";

        showToast("Target dikunci! Buddy-mu sudah diberi tahu.");

        // Simulasi Notifikasi dari Buddy (muncul setelah 3 detik)
        setTimeout(() => {
            document.getElementById('buddyAlert').style.display = 'block';
        }, 3000);
    }

    // Fungsi Validasi Buddy (Gamifikasi)
    function validateBuddy() {
        // Tambah Poin
        tengkaPoints += 10;
        focusMinutes += 45; // Asumsi 1 sesi fokus 45 menit
        
        // Update UI Angka
        document.getElementById('tengkaPoints').innerText = tengkaPoints;
        document.getElementById('focusMinutes').innerText = focusMinutes;
        
        // Sembunyikan alert
        document.getElementById('buddyAlert').style.display = 'none';
        
        // Beri apresiasi
        showToast("Validasi sukses! Tengka terjaga. +10 Poin 🌟");
    }

    // Fungsi memunculkan pesan pop-up
    function showToast(message) {
        const toast = document.getElementById('toastMsg');
        toast.innerText = message;
        toast.classList.add('show');
        setTimeout(() => {
            toast.classList.remove('show');
        }, 3000);
    }
</script>

</body>
</html>
