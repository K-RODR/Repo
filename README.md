<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
  <title>JEE Planner — Modern</title>
  <meta name="description" content="Modern JEE planner with doubts, homework, planner, deadlines, analytics and interactive charts. Single-file and GitHub Pages ready.">
  <script src="https://cdn.tailwindcss.com"></script>
  <script>tailwind.config={theme:{extend:{colors:{brand:{50:'#f2fbff',100:'#e6f7ff',200:'#bfeaff',300:'#99dcff',400:'#4fc1ff',500:'#1aa8ff',600:'#0b8de6',700:'#0670b4',800:'#054f7f',900:'#053a5c'}}}}}</script>
  <script src="https://unpkg.com/lucide@latest"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    .glass { background: linear-gradient(135deg, rgba(255,255,255,0.6), rgba(255,255,255,0.45)); backdrop-filter: blur(6px); }
    .card { transition: transform .12s ease, box-shadow .12s ease; }
    .card:hover { transform: translateY(-6px); box-shadow: 0 18px 40px rgba(2,6,23,.12); }
    .badge-overdue{ background:#ffe4e6; color:#9b111e; padding:.15rem .45rem; border-radius:999px; font-weight:600 }
    .badge-today{ background:#fff7ed; color:#92400e; padding:.15rem .45rem; border-radius:999px; font-weight:600 }
    .badge-upcoming{ background:#ecfeff; color:#075985; padding:.15rem .45rem; border-radius:999px; font-weight:600 }
    .hidden { display: none; }
    button, input, select, textarea { font-size: 1.1em; min-height: 44px; }
    label { font-size: 1em; }
  </style>
  <script src="https://accounts.google.com/gsi/client" async defer></script>
  <script src="https://apis.google.com/js/api.js"></script>
</head>
<body class="bg-slate-50 text-slate-900">
  <div class="max-w-6xl mx-auto p-4 md:p-8">
    <header class="flex items-center justify-between gap-4 mb-6">
      <div class="flex items-center gap-3">
        <div class="h-12 w-12 rounded-2xl bg-brand-600 grid place-items-center text-white text-2xl font-bold">J</div>
        <div>
          <h1 class="text-2xl md:text-3xl font-extrabold">JEE Planner — Study Companion</h1>
          <p class="text-sm text-slate-500">Doubts • Homework • Planner • Deadlines • Analytics</p>
        </div>
      </div>
      <div class="flex items-center gap-2">
        <button id="exportBtn" class="px-3 py-2 rounded-xl bg-slate-800 text-white">Export</button>
        <label class="px-3 py-2 rounded-xl bg-slate-100 cursor-pointer"><input id="importInput" type="file" accept="application/json" class="hidden">Import</label>
        <button id="themeBtn" class="px-3 py-2 rounded-xl bg-white border">Toggle Theme</button>
        <button id="signInBtn" class="px-3 py-2 rounded-xl bg-brand-600 text-white">Sign in with Google</button>
        <button id="signOutBtn" class="px-3 py-2 rounded-xl bg-slate-100 hidden">Sign out</button>
        <span id="userEmail" class="ml-2 text-sm text-slate-500"></span>
      </div>
    </header>
    <nav class="grid grid-cols-4 gap-3 mb-6">
      <button data-tab="dashboard" class="tab card p-3 rounded-2xl bg-white">🏠 Dashboard</button>
      <button data-tab="doubts" class="tab card p-3 rounded-2xl bg-white">❓ Doubts</button>
      <button data-tab="homework" class="tab card p-3 rounded-2xl bg-white">📝 Homework</button>
      <button data-tab="planner" class="tab card p-3 rounded-2xl bg-white">✅ Planner</button>
    </nav>
    <main>
      <!-- Dashboard Section -->
      <!-- ... [Unchanged: your dashboard HTML] ... -->
      <section id="dashboard" class="tab-pane">
        <div class="grid md:grid-cols-3 gap-4">
          <div class="md:col-span-2 bg-white p-4 rounded-2xl card glass">
            <div class="flex items-start justify-between">
              <h2 class="text-lg font-semibold">Today & Reminders</h2>
              <div class="text-sm text-slate-500">Updated: <span id="lastUpdated">-</span></div>
            </div>
            <div class="text-sm text-slate-500 mt-2" id="syncStatus">Not synced. Sign in to enable.</div>
            <div class="mt-4 grid grid-cols-1 md:grid-cols-2 gap-4">
              <div class="p-3 rounded-lg bg-slate-50">
                <h3 class="font-medium">Due / Overdue</h3>
                <ul id="dueList" class="mt-2 text-sm space-y-2"></ul>
              </div>
              <div class="p-3 rounded-lg bg-slate-50">
                <h3 class="font-medium">Revisit Doubts</h3>
                <ul id="revisitList" class="mt-2 text-sm space-y-2"></ul>
              </div>
            </div>
            <div class="mt-4">
              <h3 class="font-medium">Notes</h3>
              <p class="text-sm text-slate-500 mt-2">Click any chart to filter the lists below. Use export/import to back up.</p>
            </div>
            <div class="mt-4 grid md:grid-cols-3 gap-3">
              <div class="p-3 rounded-lg bg-slate-50 text-center">
                <div class="text-xs text-slate-500">Pending Doubts</div>
                <div id="statDoubts" class="text-2xl font-bold">0</div>
              </div>
              <div class="p-3 rounded-lg bg-slate-50 text-center">
                <div class="text-xs text-slate-500">Open Homework</div>
                <div id="statHw" class="text-2xl font-bold">0</div>
              </div>
              <div class="p-3 rounded-lg bg-slate-50 text-center">
                <div class="text-xs text-slate-500">To-dos</div>
                <div id="statTodo" class="text-2xl font-bold">0</div>
              </div>
            </div>
          </div>
          <div class="bg-white p-4 rounded-2xl card glass">
            <h3 class="font-semibold">Analytics</h3>
            <div class="mt-3">
              <canvas id="pieChart" height="220"></canvas>
            </div>
            <div class="mt-3">
              <canvas id="barChart" height="160"></canvas>
            </div>
          </div>
        </div>
        <div class="mt-6 grid md:grid-cols-2 gap-4">
          <div class="bg-white p-4 rounded-2xl card">
            <h3 class="font-semibold mb-3">Recent Items</h3>
            <div id="recentList" class="text-sm space-y-2 max-h-64 overflow-auto"></div>
          </div>
          <div class="bg-white p-4 rounded-2xl card">
            <h3 class="font-semibold mb-3">Deadlines (next 14 days)</h3>
            <div id="upcomingDeadlines" class="text-sm space-y-2 max-h-64 overflow-auto"></div>
          </div>
        </div>
      </section>
      <!-- Doubts Section -->
      <!-- ... [Unchanged: your doubts HTML] ... -->
      <section id="doubts" class="tab-pane hidden">
        <div class="grid md:grid-cols-3 gap-4">
          <div class="md:col-span-2 bg-white p-4 rounded-2xl card">
            <h2 class="text-lg font-semibold">Add a Doubt</h2>
            <form id="doubtForm" class="mt-3 grid grid-cols-1 gap-2">
              <div class="grid md:grid-cols-3 gap-2">
                <select id="doubtSubject" class="p-2 rounded-lg border">
                  <option>Physics</option>
                  <option>Chemistry</option>
                  <option>Maths</option>
                </select>
                <input id="doubtTopic" placeholder="Topic / Chapter" class="p-2 rounded-lg border" />
                <input id="doubtRevisit" type="date" class="p-2 rounded-lg border" />
              </div>
              <textarea id="doubtDetail" rows="5" placeholder="Describe the doubt in detail (what you tried, where stuck)" class="p-3 rounded-lg border"></textarea>
              <div class="flex gap-2 mt-2">
                <button class="px-3 py-2 rounded-xl bg-brand-600 text-white" type="submit">Add Doubt</button>
                <button id="clearDoubt" type="button" class="px-3 py-2 rounded-xl bg-slate-100">Clear</button>
              </div>
            </form>
            <div class="mt-4">
              <div class="flex items-center justify-between">
                <h3 class="font-semibold">All Doubts</h3>
                <div class="flex items-center gap-2">
                  <select id="filterDoubtSubj" class="p-2 rounded-lg border text-sm"><option value="">All</option><option>Physics</option><option>Chemistry</option><option>Maths</option></select>
                  <select id="filterDoubtStatus" class="p-2 rounded-lg border text-sm"><option value="">Any</option><option>Unresolved</option><option>Resolved</option></select>
                  <input id="searchDoubt" placeholder="Search..." class="p-2 rounded-lg border text-sm" />
                </div>
              </div>
              <div id="doubtList" class="mt-3 text-sm space-y-2 max-h-96 overflow-auto"></div>
            </div>
          </div>
          <div class="bg-white p-4 rounded-2xl card">
            <h3 class="font-semibold">Revisit Today</h3>
            <ul id="revisitToday" class="mt-3 text-sm space-y-2"></ul>
            <hr class="my-3">
            <h3 class="font-semibold">Tips</h3>
            <p class="text-sm text-slate-500 mt-2">Write exact steps you tried, mark the revisit date, and resolve when understood. Use revisit reminders to practice spaced repetition.</p>
          </div>
        </div>
      </section>
      <!-- Homework Section -->
      <!-- ... [Unchanged: your homework HTML] ... -->
      <section id="homework" class="tab-pane hidden">
        <div class="grid md:grid-cols-3 gap-4">
          <div class="md:col-span-2 bg-white p-4 rounded-2xl card">
            <h2 class="text-lg font-semibold">Add Homework</h2>
            <form id="hwForm" class="mt-3 grid grid-cols-1 gap-2">
              <input id="hwTitle" placeholder="Title (short)" class="p-2 rounded-lg border" />
              <textarea id="hwDesc" rows="5" placeholder="Detailed description (what to solve, pages, constraints)" class="p-3 rounded-lg border"></textarea>
              <div class="grid md:grid-cols-3 gap-2">
                <select id="hwSubject" class="p-2 rounded-lg border"><option>General</option><option>Physics</option><option>Chemistry</option><option>Maths</option></select>
                <input id="hwDate" type="date" class="p-2 rounded-lg border" />
                <input id="hwDeadline" type="date" class="p-2 rounded-lg border" />
              </div>
              <div class="flex gap-2 mt-2">
                <button class="px-3 py-2 rounded-xl bg-brand-600 text-white" type="submit">Add Homework</button>
                <button id="clearHw" type="button" class="px-3 py-2 rounded-xl bg-slate-100">Clear</button>
              </div>
            </form>
            <div class="mt-4">
              <h3 class="font-semibold">All Homework</h3>
              <div id="hwList" class="mt-3 text-sm space-y-2 max-h-96 overflow-auto"></div>
            </div>
          </div>
          <div class="bg-white p-4 rounded-2xl card">
            <h3 class="font-semibold">Homework Analytics</h3>
            <canvas id="hwPie" height="200"></canvas>
            <div class="mt-3 text-sm text-slate-500">Click a slice to filter homework by status.</div>
          </div>
        </div>
      </section>
      <!-- Planner Section -->
      <!-- ... [Unchanged: your planner HTML] ... -->
      <section id="planner" class="tab-pane hidden">
        <div class="grid md:grid-cols-3 gap-4">
          <div class="md:col-span-2 bg-white p-4 rounded-2xl card">
            <h2 class="text-lg font-semibold">Add Task / To‑do</h2>
            <form id="todoForm" class="mt-3 grid grid-cols-1 gap-2">
              <input id="todoTitle" placeholder="Task title" class="p-2 rounded-lg border" />
              <textarea id="todoDesc" rows="3" placeholder="Details / expected time" class="p-3 rounded-lg border"></textarea>
              <div class="grid md:grid-cols-3 gap-2">
                <select id="todoPriority" class="p-2 rounded-lg border"><option>High</option><option>Medium</option><option>Low</option></select>
                <input id="todoDate" type="date" class="p-2 rounded-lg border" />
                <input id="todoDeadline" type="date" class="p-2 rounded-lg border" />
              </div>
              <div class="flex gap-2 mt-2">
                <button class="px-3 py-2 rounded-xl bg-brand-600 text-white" type="submit">Add Task</button>
                <button id="clearTodo" type="button" class="px-3 py-2 rounded-xl bg-slate-100">Clear</button>
              </div>
            </form>
            <div class="mt-4">
              <h3 class="font-semibold">All Tasks</h3>
              <div id="taskList" class="mt-3 text-sm space-y-2 max-h-96 overflow-auto"></div>
            </div>
          </div>
          <div class="bg-white p-4 rounded-2xl card">
            <h3 class="font-semibold">Focus Tracking</h3>
            <canvas id="focusLine" height="160"></canvas>
            <div class="mt-3 text-sm text-slate-500">Pomodoro minutes get added to Focus analytics when you complete a session.</div>
            <div class="mt-3">
              <label class="text-sm">Focus min<input id="pomoFocus" type="number" value="25" class="w-full mt-1 p-2 rounded-lg border" /></label>
              <label class="text-sm mt-2">Break min<input id="pomoBreak" type="number" value="5" class="w-full mt-1 p-2 rounded-lg border" /></label>
              <div class="mt-3 flex gap-2"><button id="pomoStart" class="px-3 py-2 rounded-xl bg-brand-600 text-white">Start</button><button id="pomoStop" class="px-3 py-2 rounded-xl bg-slate-100">Stop</button></div>
            </div>
          </div>
        </div>
      </section>
    </main>
    <footer class="mt-8 text-center text-sm text-slate-500">Made for JEE aspirants • Single file • Deploy on GitHub Pages</footer>
  </div>
  <script>
    // --------- Google Auth & Drive Sync section (FIXED for true cross-device sync) ---------
    const CLIENT_ID = "513988292696-si4qkesgks11ohecii6o6frknsnjka71.apps.googleusercontent.com";
    const API_KEY = "AIzaSyB57eUdyuw5pb_2XTXS_qX0gry4YkslpHQ";
    const SCOPES = "https://www.googleapis.com/auth/drive.file";
    const SYNC_FILE_NAME = "jee_planner_data.json";
    let tokenClient, gapiInited = false, userEmail = "";

    // App state (local cache, replaced by Drive state if signed in)
    const saveStateToLocal = s => localStorage.setItem('jee.v2', JSON.stringify(s));
    const loadStateFromLocal = () => JSON.parse(localStorage.getItem('jee.v2') || '{}');
    const initialState = { doubts:[], homework:[], todos:[], analytics:{focusByDay:{}}, lastUpdated:null };
    const state = Object.assign({}, initialState, loadStateFromLocal());
    const $ = (sel,el=document)=>el.querySelector(sel);
    const $$ = (sel,el=document)=>Array.from(el.querySelectorAll(sel));
    const uid = ()=>Math.random().toString(36).slice(2,9);
    const todayStr = d=> (d||new Date()).toISOString().slice(0,10);

    window.onload = () => {
      gapi.load('client', async () => {
        await gapi.client.init({ apiKey: API_KEY, discoveryDocs: ["https://www.googleapis.com/discovery/v1/apis/drive/v3/rest"] });
        gapiInited = true;
        // If already signed in on this device, always load from Drive first
        if (gapi.client.getToken()) {
          await loadFromDrive();
        } else {
          renderAll();
        }
      });
      tokenClient = google.accounts.oauth2.initTokenClient({
        client_id: CLIENT_ID,
        scope: SCOPES,
        callback: async (tokenResponse) => {
          if(tokenResponse && tokenResponse.access_token){
            gapi.client.setToken(tokenResponse);
            await fetchUserProfile();
            showLoginStatus(true);
            await loadFromDrive();
          }
        }
      });
    };

    function showLoginStatus(isSignedIn) {
      $('#signInBtn').classList.toggle('hidden', isSignedIn);
      $('#signOutBtn').classList.toggle('hidden', !isSignedIn);
      $('#userEmail').textContent = isSignedIn ? `Logged in as: ${userEmail}` : "";
      const syncStatus = $('#syncStatus');
      if(syncStatus) syncStatus.textContent = isSignedIn ? 'Synced with Drive ✅' : 'Not synced. Sign in to enable.';
    }

    async function fetchUserProfile() {
      try {
        const res = await gapi.client.drive.about.get({ fields: "user" });
        userEmail = res.result.user.emailAddress || "";
      } catch (e) {
        userEmail = "";
      }
    }

    $('#signInBtn').addEventListener('click', () => {
      tokenClient.requestAccessToken();
    });
    $('#signOutBtn').addEventListener('click', () => {
      google.accounts.oauth2.revoke(gapi.client.getToken().access_token, () => {
        gapi.client.setToken('');
        userEmail = "";
        showLoginStatus(false);
        // reload from localStorage
        for (const k in state) delete state[k];
        Object.assign(state, initialState, loadStateFromLocal());
        renderAll();
      });
    });

    async function findFileInDrive() {
      try {
        const response = await gapi.client.drive.files.list({
          q: `'appDataFolder' in parents and name = '${SYNC_FILE_NAME}'`,
          spaces: 'appDataFolder',
          fields: 'files(id, name)',
        });
        return response.result.files.length > 0 ? response.result.files[0].id : null;
      } catch (error) {
        return null;
      }
    }

    async function loadFromDrive() {
      const statusEl = $('#syncStatus');
      if(statusEl) statusEl.textContent = 'Restoring from Drive...';
      try {
        const fileId = await findFileInDrive();
        if (fileId) {
          const response = await gapi.client.drive.files.get({
            fileId: fileId,
            alt: 'media'
          });
          const driveState = JSON.parse(response.body);
          // Completely replace app state with Drive state!
          for (const k in state) delete state[k];
          Object.assign(state, driveState);
          saveStateToLocal(state); // update localStorage with Drive state
          renderAll();
          if(statusEl) statusEl.textContent = 'Synced with Drive ✅';
          console.log("Drive state loaded:", driveState);
        } else {
          if(statusEl) statusEl.textContent = 'No file found on Drive. Using local data.';
          renderAll();
        }
      } catch (error) {
        if(statusEl) statusEl.textContent = 'Sync failed. Check console.';
        console.error("Drive sync error:", error);
        renderAll();
      }
    }

    async function saveToDrive() {
      const statusEl = $('#syncStatus');
      if(statusEl) statusEl.textContent = 'Syncing...';
      try {
        const fileId = await findFileInDrive();
        const content = JSON.stringify(state, null, 2);
        const metadata = { name: SYNC_FILE_NAME, mimeType: 'application/json' };
        if (!fileId) metadata.parents = ['appDataFolder'];
        const form = new FormData();
        form.append('metadata', new Blob([JSON.stringify(metadata)], { type: 'application/json' }));
        form.append('file', new Blob([content], { type: 'application/json' }));
        const method = fileId ? 'PATCH' : 'POST';
        const path = fileId ? `/upload/drive/v3/files/${fileId}` : '/upload/drive/v3/files';
        await gapi.client.request({
          path: path,
          method: method,
          params: { uploadType: 'multipart' },
          headers: { 'Content-Type': 'multipart/related;' },
          body: form
        });
        if(statusEl) statusEl.textContent = 'Synced with Drive ✅';
        console.log("Synced with Drive ✅");
      } catch (error) {
        if(statusEl) statusEl.textContent = 'Sync failed. Check console.';
        console.error("Save to Drive error:", error);
      }
    }

    // Save state to local and drive, sync for all changes (doubts, homework, todos)
    const saveState = () => {
      saveStateToLocal(state);
      if (gapi.client.getToken()) saveToDrive();
    };

    function initGoogleAuthUI(){ showLoginStatus(!!gapi.client.getToken()); }
    document.addEventListener("DOMContentLoaded", initGoogleAuthUI);

    // --------- App logic: ALL state changes use saveState() for sync ---------
    function addDoubtEntry(o){ o.id = uid(); o.status = o.status||'Unresolved'; o.date = todayStr(); state.doubts.unshift(o); state.lastUpdated = new Date().toISOString(); saveState(); renderDoubts(); renderDashboard(); }
    function addHwEntry(o){ o.id = uid(); o.done = false; o.date = todayStr(); state.homework.unshift(o); state.lastUpdated = new Date().toISOString(); saveState(); renderHW(); renderDashboard(); }
    function addTodoEntry(o){ o.id = uid(); o.done = false; o.date = todayStr(); state.todos.unshift(o); state.lastUpdated = new Date().toISOString(); saveState(); renderTodos(); renderDashboard(); }

    $('#doubtForm').addEventListener('submit', e=>{ e.preventDefault(); const subj=$('#doubtSubject').value; const topic=$('#doubtTopic').value.trim(); const revisit=$('#doubtRevisit').value||(()=>{ const dt=new Date(); dt.setDate(dt.getDate()+2); return dt.toISOString().slice(0,10); })(); const detail=$('#doubtDetail').value.trim(); addDoubtEntry({subject:subj, topic, revisit, detail}); e.target.reset(); });
    $('#hwForm').addEventListener('submit', e=>{ e.preventDefault(); const title=$('#hwTitle').value.trim(); const desc=$('#hwDesc').value.trim(); const subject=$('#hwSubject').value; const date=$('#hwDate').value||todayStr(); const deadline=$('#hwDeadline').value||null; if(!title) return alert('Give a title'); addHwEntry({title, desc, subject, date, deadline}); e.target.reset(); });
    $('#todoForm').addEventListener('submit', e=>{ e.preventDefault(); const title=$('#todoTitle').value.trim(); const desc=$('#todoDesc').value.trim(); const priority=$('#todoPriority').value; const date=$('#todoDate').value||todayStr(); const deadline=$('#todoDeadline').value||null; if(!title) return alert('Enter title'); addTodoEntry({title, desc, priority, date, deadline}); e.target.reset(); });

    // ... [Unchanged: rest of your rendering and analytics logic] ...
    // All delete, mark-done, etc. handlers must call saveState() (already in your code)

    // On page load, always use Drive if signed in
    if (gapiInited && gapi.client.getToken()) {
      loadFromDrive();
    } else {
      renderAll();
    }
  </script>
</body>
</html>
