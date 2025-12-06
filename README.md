
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Chiraipara Mosque Finance Department</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Playfair+Display:wght@700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.6.0/css/all.min.css">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <!-- Firebase Compat SDK -->
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
    <h1>Chiraipara Mosque Finance Department</h1>
    <p class="tagline">Donation & Expense Management System (Firestore)</p>
    <div class="auth">
      <span id="status">Viewer Mode</span>
      <button id="loginBtn" class="btn btn-success">Admin Login</button>
      <button id="logoutBtn" class="btn btn-danger hidden">Logout</button>
    </div>
  </header>

  <div class="grid">
    <main>
      <div class="card hidden" id="adminPanel">
        <h2>Add New Transaction</h2>
        <div class="form-grid">
          <select id="type">
            <option value="income">Income (Donation)</option>
            <option value="expense">Expense</option>
          </select>
          <select id="category"></select>
          <input type="number" id="amount" placeholder="Amount (BDT)" min="1">
          <input type="date" id="date">
          <input type="text" id="note" placeholder="Note (Optional)" style="grid-column: 1 / -1;">
        </div>
        <div style="display:flex; gap:8px;">
          <button id="addBtn" class="btn btn-success">Add</button>
          <button id="sampleBtn" class="btn">Add Sample Data</button>
        </div>
      </div>

      <div class="card">
        <h2>Filter</h2>
        <div style="display:flex; gap:10px; flex-wrap:wrap; align-items:end;">
          <select id="filter">
            <option value="all">All Time</option>
            <option value="month">This Month</option>
            <option value="last">Last Month</option>
            <option value="year">This Year</option>
            <option value="custom">Custom Range</option>
          </select>
          <select id="categoryFilter"><option value="all">All Categories</option></select>
          <div style="display:flex; gap:8px;">
            <input type="date" id="fromDate" class="hidden">
            <input type="date" id="toDate" class="hidden">
            <button id="applyFilter" class="btn">Apply</button>
            <button id="exportCSV" class="btn">Export CSV</button>
          </div>
        </div>
      </div>

      <div class="card">
        <h2>Transaction List</h2>
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
        <h2>Summary</h2>
        <div class="summary-grid">
          <div class="sum"><div>Total Income</div><strong id="totalIncome">0</strong> BDT</div>
          <div class="sum"><div>Total Expense</div><strong id="totalExpense">0</strong> BDT</div>
          <div class="sum" style="background: linear-gradient(135deg, #d4f4dd, #a7f3d0);"><div>Balance</div><strong id="balance">0</strong> BDT</div>
        </div>
      </div>

      <div class="card hidden" id="categoryManager">
        <h2>Manage Categories</h2>
        <div style="display:flex; gap:8px; margin-bottom:10px;">
          <input type="text" id="newCategory" placeholder="New Category Name">
          <button id="addCategory" class="btn btn-success">Add</button>
        </div>
        <div id="categoryList"></div>
      </div>

      <div class="card">
        <h2>Income Chart (Last 6 Months)</h2>
        <canvas id="incomeChart" height="200"></canvas>
      </div>
    </aside>
  </div>
</div>

<div id="loginModal" class="modal hidden">
  <div class="modal-box">
    <h2 style="color:var(--primary);">Admin Login</h2>
    <input type="password" id="passwordInput" placeholder="Enter Password" style="width:100%; padding:10px; margin:12px 0; border-radius:8px; border:1px solid #ccc;">
    <div style="display:flex; gap:8px; justify-content:center;">
      <button id="loginConfirm" class="btn btn-success">Login</button>
      <button id="loginCancel" class="btn">Cancel</button>
    </div>
    <small style="display:block; margin-top:12px; color:#888;">Hint: Contact mosque admin for password</small>
  </div>
</div>

<script>
  // Simple hash function (djb2)
  function hash(str) {
    let h = 5381;
    for (let i = 0; i < str.length; i++) {
      h = ((h << 5) + h) + str.charCodeAt(i); /* h * 33 + c */
    }
    return h >>> 0;
  }

  // Hashed version of the real password "Ahosan" → 4055642719
  const CORRECT_HASH = 4055642719;

  // Firebase Initialize
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
  let allCategories = ['Jummah Donation','Monthly Fund','Ramadan Fund','Zakat','Sadaqah','Maintenance','Electricity & Utility','Imam Salary','Construction','Eid Fund'];

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

  // Secure Login System (Password is now hidden)
  els.loginBtn.onclick = () => els.modal.classList.remove('hidden');
  els.loginCancel.onclick = () => { els.modal.classList.add('hidden'); els.passwordInput.value = ''; };
  els.loginConfirm.onclick = () => {
    const inputHash = hash(els.passwordInput.value.trim());
    if (inputHash === CORRECT_HASH) {
      isAdmin = true;
      els.modal.classList.add('hidden');
      els.status.textContent = 'Admin Mode';
      els.loginBtn.classList.add('hidden');
      els.logoutBtn.classList.remove('hidden');
      els.adminPanel.classList.remove('hidden');
      els.categoryManager.classList.remove('hidden');
      alert("Login successful!");
    } else {
      alert('Wrong password!');
    }
    els.passwordInput.value = '';
  };

  els.logoutBtn.onclick = () => {
    isAdmin = false;
    els.status.textContent = 'Viewer Mode';
    els.logoutBtn.classList.add('hidden');
    els.loginBtn.classList.remove('hidden');
    els.adminPanel.classList.add('hidden');
    els.categoryManager.classList.add('hidden');
  };

  // Load & Save Categories
  async function loadCategories() {
    try {
      const doc = await db.collection('mosqueCategories').doc('list').get();
      if (doc.exists) allCategories = doc.data().items || allCategories;
    } catch (e) { console.log("Using default categories"); }
    populateCategories();
    renderCategories();
  }

  async function saveCategories() {
    await db.collection('mosqueCategories').doc('list').set({ items: allCategories });
  }

  function populateCategories() {
    [els.category, els.categoryFilter].forEach(sel => {
      sel.innerHTML = sel === els.categoryFilter ? '<option value="all">All Categories</option>' : '';
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
        btn.className = 'btn btn-danger'; btn.textContent = 'Delete';
        btn.onclick = async () => {
          if (confirm('Delete this category?')) {
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
    if (!name) return alert('Enter category name');
    if (allCategories.includes(name)) return alert('Already exists');
    allCategories.push(name);
    await saveCategories();
    els.newCategory.value = '';
    populateCategories();
    renderCategories();
  };

  // Real-time Transactions
  db.collection('mosqueTransactions')
    .orderBy('date', 'desc')
    .onSnapshot(snap => {
      allTransactions = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      render();
    });

  els.date.valueAsDate = new Date();

  els.addBtn.onclick = async () => {
    const amt = parseFloat(els.amount.value);
    if (!amt || amt <= 0) return alert('Enter valid amount');
    await db.collection('mosqueTransactions').add({
      type: els.type.value,
      category: els.category.value,
      amount: amt,
      date: els.date.value,
      note: els.note.value.trim(),
      timestamp: firebase.firestore.FieldValue.serverTimestamp()
    });
    els.amount.value = ''; 
    els.note.value = '';
  };

  els.sampleBtn.onclick = async () => {
    if (!confirm('Add sample data?')) return;
    const samples = [
      {type:'income', category:'Jummah Donation', amount:12500, date:'2025-11-15', note:'Friday Collection'},
      {type:'income', category:'Ramadan Fund', amount:45000, date:'2025-04-05', note:'Ramadan 2025'},
      {type:'expense', category:'Electricity & Utility', amount:8900, date:'2025-11-01', note:'Monthly Bill'}
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

    els.tableHead.innerHTML = `
      <tr>
        <th>Date</th>
        <th>Type</th>
        <th>Category</th>
        <th>Amount</th>
        <th>Note</th>
        ${isAdmin ? '<th>Action</th>' : ''}
      </tr>
    `;

    els.tableBody.innerHTML = '';
    list.forEach(t => {
      const row = els.tableBody.insertRow();
      const typeText = t.type === 'income' ? 'Income' : 'Expense';
      const typeColor = t.type === 'income' ? '#10b981' : '#ef4444';

      row.innerHTML = `
        <td>${t.date || 'N/A'}</td>
        <td style="color:\( {typeColor}; font-weight:600;"> \){typeText}</td>
        <td>${t.category || 'Unknown'}</td>
        <td>${Number(t.amount).toLocaleString()} BDT</td>
        <td>${t.note || '-'}</td>
        ${isAdmin ? '<td></td>' : ''}
      `;

      if (isAdmin) {
        const deleteBtn = document.createElement('button');
        deleteBtn.className = 'btn btn-danger';
        deleteBtn.textContent = 'Delete';
        deleteBtn.style.cssText = 'padding:4px 8px; font-size:13px;';
        deleteBtn.onclick = () => {
          if (confirm('Delete this transaction?')) {
            db.collection('mosqueTransactions').doc(t.id).delete();
          }
        };
        row.cells[row.cells.length - 1].appendChild(deleteBtn);
      }
    });

    const totalInc = list.filter(t => t.type === 'income').reduce((a, t) => a + Number(t.amount), 0);
    const totalExp = list.filter(t => t.type === 'statement').reduce((a, t) => a + Number(t.amount), 0);

    els.totalIncome.textContent = totalInc.toLocaleString();
    els.totalExpense.textContent = totalExp.toLocaleString();
    els.balance.textContent = (totalInc - totalExp).toLocaleString();

    // Income Chart (Last 6 Months)
    const monthly = {};
    allTransactions.filter(t => t.type === 'income').forEach(t => {
      const monthKey = t.date ? t.date.slice(0, 7) : '';
      if (monthKey) monthly[monthKey] = (monthly[monthKey] || 0) + Number(t.amount);
    });

    const labels = Object.keys(monthly).sort().slice(-6);
    const values = labels.map(m => monthly[m] || 0);
    const displayLabels = labels.map(m => new Date(m + '-01').toLocaleDateString('en-US', { month: 'short', year: 'numeric' }));

    if (chart) chart.destroy();
    chart = new Chart(document.getElementById('incomeChart'), {
      type: 'bar',
      data: {
        labels: displayLabels,
        datasets: [{
          label: 'Income',
          data: values,
          backgroundColor: 'rgba(59, 130, 246, 0.7)'
        }]
      },
      options: {
        responsive: true,
        plugins: { legend: { display: false } },
        scales: { y: { beginAtZero: true } }
      }
    });
  }

  // Filter Controls
  els.filter.onchange = () => {
    const isCustom = els.filter.value === 'custom';
    els.fromDate.classList.toggle('hidden', !isCustom);
    els.toDate.classList.toggle('hidden', !isCustom);
  };

  els.applyFilter.onclick = render;

  els.exportCSV.onclick = () => {
    let csv = 'Date,Type,Category,Amount,Note\n';
    getFiltered().forEach(t => {
      const type = t.type === 'income' ? 'Income' : 'Expense';
      const note = (t.note || '').replace(/"/g, '""');
      csv += `\( {t.date}, \){type},\( {t.category}, \){t.amount},"${note}"\n`;
    });
    const blob = new Blob(['\uFEFF' + csv], { type: 'text/csv;charset=utf-8' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'Mosque_Finance_Report.csv';
    a.click();
  };

  // Initialize
  loadCategories();
</script>
</body>
</html>
