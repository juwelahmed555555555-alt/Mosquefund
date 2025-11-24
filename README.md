# Mosquefund
Fund 
<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>চিড়াইপাড়া মসজিদ অর্থবিভাগ</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Playfair+Display:wght@700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.6.0/css/all.min.css">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <!-- Firebase Compat SDK (LLFC-এর মতোই) -->
  <script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-firestore-compat.js"></script>

  <style>
    :root { --primary: #1e40af; --light: #3b82f6; --success: #10b981; --danger: #ef4444; --card: #ffffff; --text: #1e293b; --muted: #64748b; --border: #e2e8f0; }
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: 'Inter', sans-serif; background: linear-gradient(135deg, #dbeafe, #eef2ff); color: var(--text); min-height: 100vh; }
    .container { max-width: 1200px; margin: 20px auto; padding: 20px; }
    header { background: var(--card); padding: 20px; border-radius: 12px; box-shadow: 0 8px 24px rgba(30,64,175,.08); text-align: center; position: relative; margin-bottom: 20px; }
    .logo { width: 76px; height: 76px; background: var(--primary); color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 34px; margin: 0 auto 10px; }
    h1 { font-family: 'Playfair Display', serif; font-size: 28px; color: var(--primary); margin: 6px 0; }
    .tagline { color: var(--muted); font-size: 14px; }
    .auth { position: absolute; top: 16px; right: 16px; background: #f1f5f9; padding: 8px 14px; border-radius: 10px; display: flex; gap: 10px; align-items: center; font-size: 13px; }
    .btn { padding: 8px 14px; border: none; border-radius: 10px; color: white; cursor: pointer; font-weight: 600; transition: all 0.2s; }
    .btn:hover { transform: translateY(-2px); }
    .btn-success { background: var(--success); }
    .btn-danger { background: var(--danger); }
    .grid { display: grid; grid-template-columns: 1fr 380px; gap: 18px; }
    .card { background: var(--card); padding: 18px; border-radius: 12px; box-shadow: 0 6px 18px rgba(0,0,0,.06); margin-bottom: 16px; }
    h2 { font-size: 18px; color: var(--primary); border-bottom: 2px solid var(--light); padding-bottom: 8px; margin-bottom: 12px; }
    .form-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(160px, 1fr)); gap: 10px; margin-bottom: 12px; }
    input, select { padding: 10px; border: 1px solid var(--border); border-radius: 8px; width: 100%; font-size: 14px; }
    input:focus, select:focus { outline: none; border-color: var(--light); box-shadow: 0 0 0 3px rgba(59,130,246,.12); }
    table { width: 100%; border-collapse: collapse; margin-top: 10px; font-size: 14px; }
    th, td { padding: 10px; text-align: left; border-bottom: 1px solid var(--border); }
    th { background: #f8fafc; color: var(--primary); font-weight: 600; }
    .hidden { display: none !important; }
    .modal { position: fixed; inset: 0; background: rgba(0,0,0,.6); display: flex; align-items: center; justify-content: center; z-index: 999; }
    .modal-box { background: white; padding: 22px; border-radius: 12px; width: 92%; max-width: 420px; text-align: center; }
    .summary-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); gap: 10px; }
    .sum { background: linear-gradient(135deg, #dbeafe, #bfdbfe); padding: 14px; border-radius: 8px; text-align: center; color: var(--primary); }
    .sum strong { font-size: 20px; display: block; margin-top: 6px; }
    @media (max-width: 968px) { .grid { grid-template-columns: 1fr; } .auth { position: static; justify-content: center; margin-top: 10px; } }
  </style>
</head>
<body>

<div class="container">
  <header>
    <div class="logo"><i class="fas fa-mosque"></i></div>
    <h1>চিড়াইপাড়া মসজিদ অর্থবিভাগ</h1>
    <p class="tagline">দান ও ব্যয় ব্যবস্থাপনা সিস্টেম (Firestore)</p>
    <div class="auth">
      <span id="status">দর্শক মোড</span>
      <button id="loginBtn" class="btn btn-success">অ্যাডমিন লগইন</button>
      <button id="logoutBtn" class="btn btn-danger hidden">লগআউট</button>
    </div>
  </header>

  <div class="grid">
    <main>
      <div class="card hidden" id="adminPanel">
        <h2>নতুন লেনদেন যোগ করুন</h2>
        <div class="form-grid">
          <select id="type"><option value="income">আয় (দান)</option><option value="expense">ব্যয়</option></select>
          <select id="category"></select>
          <input type="number" id="amount" placeholder="টাকার পরিমাণ (BDT)" min="1">
          <input type="date" id="date">
          <input type="text" id="note" placeholder="নোট (ঐচ্ছিক)" style="grid-column: 1 / -1;">
        </div>
        <div style="display:flex; gap:8px;">
          <button id="addBtn" class="btn btn-success">যোগ করুন</button>
          <button id="sampleBtn" class="btn">স্যাম্পল ডাটা</button>
        </div>
      </div>

      <div class="card">
        <h2>ফিল্টার</h2>
        <div style="display:flex; gap:10px; flex-wrap:wrap; align-items:end;">
          <select id="filter">
            <option value="all">সমস্ত সময়</option>
            <option value="month">এই মাস</option>
            <option value="last">গত মাস</option>
            <option value="year">এই বছর</option>
            <option value="custom">কাস্টম রেঞ্জ</option>
          </select>
          <select id="categoryFilter"><option value="all">সব ক্যাটাগরি</option></select>
          <div style="display:flex; gap:8px;">
            <input type="date" id="fromDate" class="hidden">
            <input type="date" id="toDate" class="hidden">
            <button id="applyFilter" class="btn">প্রয়োগ</button>
            <button id="exportCSV" class="btn">CSV এক্সপোর্ট</button>
          </div>
        </div>
      </div>

      <div class="card">
        <h2>লেনদেনের তালিকা</h2>
        <div style="overflow-x:auto;">
          <table>
            <thead id="tableHead"></thead>
            <tbody id="tableBody"></tbody>
          </table>
        </div>
      </div>
    </main>

    <aside>
      <div class="card">
        <h2>সারাংশ</h2>
        <div class="summary-grid">
          <div class="sum"><div>মোট আয়</div><strong id="totalIncome">0</strong> BDT</div>
          <div class="sum"><div>মোট ব্যয়</div><strong id="totalExpense">0</strong> BDT</div>
          <div class="sum" style="background: linear-gradient(135deg, #d4f4dd, #a7f3d0);"><div>ব্যালেন্স</div><strong id="balance">0</strong> BDT</div>
        </div>
      </div>

      <div class="card hidden" id="categoryManager">
        <h2>ক্যাটাগরি পরিচালনা</h2>
        <div style="display:flex; gap:8px; margin-bottom:10px;">
          <input type="text" id="newCategory" placeholder="নতুন ক্যাটাগরি নাম">
          <button id="addCategory" class="btn btn-success">যোগ</button>
        </div>
        <div id="categoryList"></div>
      </div>

      <div class="card">
        <h2>আয় গ্রাফ (সর্বশেষ ৬ মাস)</h2>
        <canvas id="incomeChart" height="200"></canvas>
      </div>
    </aside>
  </div>
</div>

<div id="loginModal" class="modal hidden">
  <div class="modal-box">
    <h2 style="color:var(--primary);">অ্যাডমিন লগইন</h2>
    <input type="password" id="passwordInput" placeholder="পাসওয়ার্ড দিন" style="width:100%; padding:10px; margin:12px 0; border-radius:8px; border:1px solid #ccc;">
    <div style="display:flex; gap:8px; justify-content:center;">
      <button id="loginConfirm" class="btn btn-success">লগইন</button>
      <button id="loginCancel" class="btn">বাতিল</button>
    </div>
    <small style="display:block; margin-top:12px; color:#666;">পাসওয়ার্ড: <b>ahosan</b></small>
  </div>
</div>

<script>
  // Firebase Initialize - শুধুমাত্র এখানেই db আছে
  const firebaseConfig = {
    apiKey: "AIzaSyCsZrHcpJgGoTHeW0Ex4Hv20KLtDopPq4",
    authDomain: "llfc-4d2df.firebaseapp.com",
    projectId: "llfc-4d2df",
    storageBucket: "llfc-4d2df.firebasestorage.app",
    messagingSenderId: "697058785471",
    appId: "1:697058785471:web:7481cae8fe6b682d762e0a"
  };

  firebase.initializeApp(firebaseConfig);
  const db = firebase.firestore();

  let isAdmin = false;
  let chart = null;
  let allTransactions = [];
  let allCategories = ['জুমা দান','মাসিক ফান্ড','রামাদান ফান্ড','যাকাত','সাদাকাহ','রক্ষণাবেক্ষণ','বিদ্যুৎ ও ইউটিলিটি','ইমাম বেতন','নির্মাণ','ঈদ ফান্ড'];

  const els = {
    status: document.getElementById('status'),
    loginBtn: document.getElementById('loginBtn'),
    logoutBtn: document.getElementById('logoutBtn'),
    adminPanel: document.getElementById('adminPanel'),
    categoryManager: document.getElementById('categoryManager'),
    modal: document.getElementById('loginModal'),
    passwordInput: document.getElementById('passwordInput'),
    loginConfirm: document.getElementById('loginConfirm'),
    loginCancel: document.getElementById('loginCancel'),
    type: document.getElementById('type'),
    category: document.getElementById('category'),
    amount: document.getElementById('amount'),
    date: document.getElementById('date'),
    note: document.getElementById('note'),
    addBtn: document.getElementById('addBtn'),
    sampleBtn: document.getElementById('sampleBtn'),
    filter: document.getElementById('filter'),
    categoryFilter: document.getElementById('categoryFilter'),
    fromDate: document.getElementById('fromDate'),
    toDate: document.getElementById('toDate'),
    applyFilter: document.getElementById('applyFilter'),
    exportCSV: document.getElementById('exportCSV'),
    tableHead: document.getElementById('tableHead'),
    tableBody: document.getElementById('tableBody'),
    totalIncome: document.getElementById('totalIncome'),
    totalExpense: document.getElementById('totalExpense'),
    balance: document.getElementById('balance'),
    newCategory: document.getElementById('newCategory'),
    addCategory: document.getElementById('addCategory'),
    categoryList: document.getElementById('categoryList')
  };

  // Login
  els.loginBtn.onclick = () => els.modal.classList.remove('hidden');
  els.loginCancel.onclick = () => { els.modal.classList.add('hidden'); els.passwordInput.value = ''; };
  els.loginConfirm.onclick = () => {
    if (els.passwordInput.value === 'ahosan') {
      isAdmin = true;
      els.modal.classList.add('hidden');
      els.status.textContent = 'অ্যাডমিন মোড';
      els.loginBtn.classList.add('hidden');
      els.logoutBtn.classList.remove('hidden');
      els.adminPanel.classList.remove('hidden');
      els.categoryManager.classList.remove('hidden');
    } else alert('ভুল পাসওয়ার্ড');
    els.passwordInput.value = '';
  };
  els.logoutBtn.onclick = () => {
    isAdmin = false;
    els.status.textContent = 'দর্শক মোড';
    els.logoutBtn.classList.add('hidden');
    els.loginBtn.classList.remove('hidden');
    els.adminPanel.classList.add('hidden');
    els.categoryManager.classList.add('hidden');
  };

  // Load Categories
  async function loadCategories() {
    try {
      const doc = await db.collection('mosqueCategories').doc('list').get();
      if (doc.exists) allCategories = doc.data().items;
    } catch (e) { console.log("Categories not found, using default"); }
    populateCategories();
    renderCategories();
  }

  async function saveCategories() {
    await db.collection('mosqueCategories').doc('list').set({ items: allCategories });
  }

  function populateCategories() {
    [els.category, els.categoryFilter].forEach(sel => {
      sel.innerHTML = sel === els.categoryFilter ? '<option value="all">সব ক্যাটাগরি</option>' : '';
      allCategories.forEach(c => sel.add(new Option(c, c)));
    });
  }

  function renderCategories() {
    els.categoryList.innerHTML = '';
    allCategories.forEach((c, i) => {
      const div = document.createElement('div');
      div.style.cssText = 'display:flex; justify-content:space-between; padding:8px 0; border-bottom:1px solid #eee; align-items:center;';
      div.innerHTML = `<span>${c}</span>`;
      if (isAdmin) {
        const btn = document.createElement('button');
        btn.className = 'btn btn-danger'; btn.textContent = 'মুছুন';
        btn.onclick = async () => {
          if (confirm('মুছবেন?')) {
            allCategories.splice(i, 1);
            await saveCategories();
            populateCategories();
            renderCategories();
          }
        };
        div.appendChild(btn);
      }
      els.categoryList.appendChild(div);
    });
  }

  els.addCategory.onclick = async () => {
    const name = els.newCategory.value.trim();
    if (!name) return alert('নাম দিন');
    if (allCategories.includes(name)) return alert('আগে থেকে আছে');
    allCategories.push(name);
    await saveCategories();
    els.newCategory.value = '';
    populateCategories();
    renderCategories();
  };

  // Load Transactions Real-time
  db.collection('mosqueTransactions')
    .orderBy('date', 'desc')
    .onSnapshot(snap => {
      allTransactions = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      render();
    });

  els.date.valueAsDate = new Date();

  els.addBtn.onclick = async () => {
    const amt = parseFloat(els.amount.value);
    if (!amt || amt <= 0) return alert('সঠিক পরিমাণ দিন');
    await db.collection('mosqueTransactions').add({
      type: els.type.value,
      category: els.category.value,
      amount: amt,
      date: els.date.value,
      note: els.note.value.trim(),
      timestamp: firebase.firestore.FieldValue.serverTimestamp()
    });
    els.amount.value = ''; els.note.value = '';
  };

  els.sampleBtn.onclick = async () => {
    if (!confirm('স্যাম্পল ডাটা যোগ করবেন?')) return;
    const samples = [
      {type:'income', category:'জুমা দান', amount:12500, date:'2025-11-15', note:'জুমার সংগ্রহ'},
      {type:'income', category:'রামাদান ফান্ড', amount:45000, date:'2025-04-05', note:'রামাদান 2025'},
      {type:'expense', category:'বিদ্যুৎ ও ইউটিলিটি', amount:8900, date:'2025-11-01', note:'মাসিক বিল'}
    ];
    for (const s of samples) {
      await db.collection('mosqueTransactions').add({ ...s, timestamp: firebase.firestore.FieldValue.serverTimestamp() });
    }
  };

  function getFiltered() {
    let list = [...allTransactions];
    const mode = els.filter.value;
    const cat = els.categoryFilter.value;
    const now = new Date();

    if (mode === 'month') list = list.filter(t => new Date(t.date).getMonth() === now.getMonth() && new Date(t.date).getFullYear() === now.getFullYear());
    else if (mode === 'last') {
      const last = new Date(now.getFullYear(), now.getMonth() - 1, 1);
      list = list.filter(t => new Date(t.date).getMonth() === last.getMonth() && new Date(t.date).getFullYear() === last.getFullYear());
    }
    else if (mode === 'year') list = list.filter(t => new Date(t.date).getFullYear() === now.getFullYear());
    else if (mode === 'custom' && els.fromDate.value && els.toDate.value) list = list.filter(t => t.date >= els.fromDate.value && t.date <= els.toDate.value);
    if (cat && cat !== 'all') list = list.filter(t => t.category === cat);
    return list.sort((a,b) => (b.date || '').localeCompare(a.date || ''));
  }

  function render() {
    const list = getFiltered();
    els.tableHead.innerHTML = `<tr><th>তারিখ</th><th>টাইপ</th><th>ক্যাটাগরি</th><th>টাকা</th><th>নোট</th>${isAdmin?'<th>ক্রিয়া</th>':''}</tr>`;
    els.tableBody.innerHTML = '';
    list.forEach(t => {
      const row = els.tableBody.insertRow();
      const color = t.type === 'income' ? '#10b981' : '#ef4444';
      row.innerHTML = `<td>\( {t.date}</td><td style="color: \){color};font-weight:600;">\( {t.type==='income'?'আয়':'ব্যয়'}</td><td> \){t.category}</td><td>\( {Number(t.amount).toLocaleString()} BDT</td><td> \){t.note||'-'}</td>${isAdmin?'<td></td>':''}`;
      if (isAdmin) {
        const del = document.createElement('button');
        del.className = 'btn btn-danger'; del.textContent = 'মুছুন'; del.style.cssText = 'padding:4px 8px; font-size:13px;';
        del.onclick = () => { if(confirm('মুছবেন?')) db.collection('mosqueTransactions').doc(t.id).delete(); };
        row.cells[row.cells.length-1].appendChild(del);
      }
    });

    const inc = list.filter(t=>t.type==='income').reduce((a,t)=>a+Number(t.amount),0);
    const exp = list.filter(t=>t.type==='expense').reduce((a,t)=>a+Number(t.amount),0);
    els.totalIncome.textContent = inc.toLocaleString();
    els.totalExpense.textContent = exp.toLocaleString();
    els.balance.textContent = (inc - exp).toLocaleString();

    // Chart
    const monthly = {};
    allTransactions.filter(t=>t.type==='income').forEach(t => {
      const m = t.date.slice(0,7);
      monthly[m] = (monthly[m]||0) + Number(t.amount);
    });
    const labels = Object.keys(monthly).sort().slice(-6);
    const values = labels.map(m => monthly[m]||0);
    const bnLabels = labels.map(m => new Date(m+'-01').toLocaleDateString('bn-BD', {month:'short', year:'numeric'}));

    if (chart) chart.destroy();
    chart = new Chart(document.getElementById('incomeChart'), {
      type: 'bar',
      data: { labels: bnLabels, datasets: [{ label: 'আয়', data: values, backgroundColor: 'rgba(59,130,246,0.7)' }] },
      options: { responsive: true, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true } } }
    });
  }

  els.filter.onchange = () => {
    const show = els.filter.value === 'custom';
    els.fromDate.classList.toggle('hidden', !show);
    els.toDate.classList.toggle('hidden', !show);
  };
  els.applyFilter.onclick = render;

  els.exportCSV.onclick = () => {
    let csv = 'তারিখ,টাইপ,ক্যাটাগরি,পরিমাণ,নোট\n';
    getFiltered().forEach(t => csv += `\( {t.date}, \){t.type==='income'?'আয়':'ব্যয়'},\( {t.category}, \){t.amount},"${(t.note||'').replace(/"/g,'""')}"\n`);
    const blob = new Blob(['\uFEFF' + csv], {type: 'text/csv;charset=utf-8'});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url; a.download = 'মসজিদ_অর্থ_রিপোর্ট.csv';
    a.click();
  };

  // Start
  loadCategories();
</script>
</body>
</html>
