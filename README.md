# Adi-s-Group
For supply Central government
https://adi-s-group.myjoonweb.com
<!doctype html>
<html lang="hi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Admission Result Search</title>
  <style>
    :root{--accent:#0b74de;--bg:#f6f9ff}
    *{box-sizing:border-box}body{margin:0;font-family:Inter,system-ui,Arial,sans-serif;background:var(--bg);color:#0f1724}
    .wrap{max-width:920px;margin:36px auto;padding:18px}
    header{display:flex;align-items:center;gap:12px}
    h1{margin:0;font-size:20px}
    .card{background:#fff;padding:18px;border-radius:12px;box-shadow:0 6px 18px rgba(11,29,50,0.06);margin-top:18px}
    form{display:flex;gap:8px;flex-wrap:wrap}
    input[type=text]{flex:1;min-width:220px;padding:12px;border-radius:8px;border:1px solid #e6eef7}
    button{padding:10px 14px;border-radius:8px;border:0;background:var(--accent);color:#fff}
    .result{margin-top:12px}
    table{width:100%;border-collapse:collapse}
    th,td{padding:10px;border-bottom:1px solid #eef6ff;text-align:left}
    .hint{font-size:13px;color:#475569}
    .small{font-size:13px;color:#6b7280}
    @media(max-width:560px){.wrap{padding:12px}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Admission Result Search</h1>
      <div class="small">Admission No. dal ke "Search" dabayein</div>
    </header>

    <div class="card">
      <form id="searchForm" onsubmit="return false;">
        <input id="admInput" type="text" placeholder="Enter Admission Number (e.g. ADM2025-001)" required />
        <button id="searchBtn">Search</button>
        <button id="allBtn" type="button">Show Sample Data</button>
      </form>

      <div class="result" id="resultArea">
        <div class="hint">Result yahan dikhai dega.</div>
      </div>

      <hr style="margin-top:14px" />
      <div class="small">Notes:
        <ul>
          <li>Ye demo client-side app hai. Agar aapke paas server/API ho to <code>fetchResultFromServer(adm)</code> ko uncomment kar ke endpoint ka URL daal dijiye.</li>
          <li>Data edit karne ke liye page ke upar <code>const SAMPLE_DB</code> object ko update kariye.</li>
        </ul>
      </div>
    </div>

    <div class="small" style="margin-top:12px">Hosting tips: Is file ko <code>index.html</code> naam se save karke GitHub Pages / Netlify / Vercel par upload kijiye.</div>
  </div>

  <script>
    // ====== SAMPLE DATA (edit/replace with your backend) ======
    const SAMPLE_DB = [
      { admission: 'ADM2025-001', name: 'Ravi Kumar', class: '10', marks: { math: 86, eng: 78, sci: 81 }, status: 'Pass' },
      { admission: 'ADM2025-002', name: 'Sita Devi', class: '10', marks: { math: 92, eng: 88, sci: 95 }, status: 'Pass' },
      { admission: 'ADM2025-003', name: 'Aman Singh', class: '10', marks: { math: 45, eng: 52, sci: 48 }, status: 'Fail' }
    ];

    // ====== UI helpers ======
    const resultArea = document.getElementById('resultArea');
    const admInput = document.getElementById('admInput');
    const searchBtn = document.getElementById('searchBtn');
    const allBtn = document.getElementById('allBtn');

    function renderStudent(student) {
      if(!student) return `<div class="small">No result found for that admission number.</div>`;
      return `
        <table>
          <tr><th>Admission No.</th><td>${escapeHtml(student.admission)}</td></tr>
          <tr><th>Name</th><td>${escapeHtml(student.name)}</td></tr>
          <tr><th>Class</th><td>${escapeHtml(student.class)}</td></tr>
          <tr><th>Status</th><td>${escapeHtml(student.status)}</td></tr>
          <tr><th>Marks</th><td>Math: ${student.marks.math} | Eng: ${student.marks.eng} | Sci: ${student.marks.sci}</td></tr>
        </table>`;
    }

    function escapeHtml(s){
      return String(s).replace(/[&<>"]+/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c]));
    }

    function searchLocal(adm){
      const a = adm.trim();
      if(!a) return null;
      return SAMPLE_DB.find(x => x.admission.toLowerCase() === a.toLowerCase()) || null;
    }

    // If you have a server API, replace URL and use this function.
    async function fetchResultFromServer(adm){
      // Example:
      // const resp = await fetch('https://example.com/api/results?admission=' + encodeURIComponent(adm));
      // if(!resp.ok) throw new Error('Server error');
      // return await resp.json();

      // For demo we return null to show how to swap in a server call.
      return null;
    }

    // Main search handler — tries server first, then local DB as fallback
    async function handleSearch(){
      const adm = admInput.value.trim();
      if(!adm){ resultArea.innerHTML = '<div class="small">Admission number daalein.</div>'; return; }

      resultArea.innerHTML = '<div class="small">Searching…</div>';

      try{
        // Uncomment next line and provide working server to use remote API
        // const serverData = await fetchResultFromServer(adm);
        const serverData = null;

        if(serverData && serverData.admission){
          resultArea.innerHTML = renderStudent(serverData);
          return;
        }

        // fallback to client-side SAMPLE_DB
        const local = searchLocal(adm);
        resultArea.innerHTML = renderStudent(local);
      }catch(err){
        resultArea.innerHTML = '<div class="small">Error searching results. Console dekhiye.</div>';
        console.error(err);
      }
    }

    searchBtn.addEventListener('click', handleSearch);
    admInput.addEventListener('keydown', e=>{ if(e.key==='Enter'){ handleSearch(); } });

    allBtn.addEventListener('click', ()=>{
      resultArea.innerHTML = '<table><tr><th>Admission</th><th>Name</th><th>Class</th><th>Status</th></tr>' +
        SAMPLE_DB.map(s=>`<tr><td>${s.admission}</td><td>${escapeHtml(s.name)}</td><td>${escapeHtml(s.class)}</td><td>${escapeHtml(s.status)}</td></tr>`).join('') +
        '</table>';
    });

    // ====== How to replace SAMPLE_DB with your real data ======
    // Option A (small dataset): edit the SAMPLE_DB array above and save file.
    // Option B (server): implement an API endpoint that returns JSON for ?admission=...
    //    Response shape (example): { admission: 'ADM2025-001', name: 'Ravi Kumar', class: '7', marks:{math:86,eng:78,sci:81}, status:'Pass' }
    //    Then implement fetchResultFromServer to call that endpoint and return parsed JSON.

  </script>
</body>
</html>
