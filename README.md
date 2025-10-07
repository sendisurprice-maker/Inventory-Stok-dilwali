<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Sistem Inventory Booth</title>

<!-- Library -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>

<style>
  /* ---------- Basic layout & colors ---------- */
  body{font-family: "Segoe UI", Arial, sans-serif;background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);margin:0;padding:20px}
  .container{max-width:1200px;margin:0 auto;background:#fff;border-radius:14px;overflow:hidden;box-shadow:0 12px 40px rgba(0,0,0,.18)}
  .header{background:#667eea;color:#fff;padding:26px;text-align:center}
  .header h1{margin:0;font-size:26px}
  .config-section{background:#fff3cd;border:2px solid #ffc107;padding:18px;margin:18px;border-radius:10px}
  .config-input{display:flex;gap:10px;align-items:center}
  .config-input label{min-width:160px;font-weight:700}
  input[type="text"]{flex:1;padding:10px;border:1px solid #d0d0d0;border-radius:8px;font-size:14px}
  .toolbar{padding:18px;display:flex;gap:12px;flex-wrap:wrap;border-bottom:1px solid #eee}
  button{padding:10px 16px;border-radius:8px;border:0;font-weight:700;cursor:pointer}
  .btn-primary{background:#667eea;color:#fff} .btn-success{background:#10b981;color:#fff}
  .btn-warning{background:#f59e0b;color:#fff} .btn-danger{background:#ef4444;color:#fff}
  .btn-info{background:#06b6d4;color:#fff}
  .summary-cards{display:flex;gap:12px;padding:18px;flex-wrap:wrap}
  .card{flex:1;background:linear-gradient(135deg,#667eea,#764ba2);color:#fff;padding:14px;border-radius:10px;text-align:center}
  table{width:100%;border-collapse:collapse;background:#fff}
  th{background:#667eea;color:#fff;padding:12px;text-align:left;position:sticky;top:0}
  td{padding:10px;border-bottom:1px solid #eee;vertical-align:middle}
  input[type="number"]{width:80px;padding:6px;border-radius:6px;border:1px solid #ddd;text-align:center}
  .badge{display:inline-block;padding:6px 10px;border-radius:12px;font-weight:700}
  .badge-success{background:#d1fae5;color:#065f46}
  .badge-warning{background:#fef3c7;color:#92400e}
  .badge-danger{background:#fee2e2;color:#991b1b}
  .loading{padding:30px;text-align:center;color:#667eea}
  /* modal */
  .modal{display:none;position:fixed;inset:0;background:rgba(0,0,0,.5);align-items:center;justify-content:center;z-index:999}
  .modal.active{display:flex}
  .modal-box{background:#fff;padding:20px;border-radius:10px;max-width:420px;width:95%}
  .form-group{margin-bottom:12px}
  @media (max-width:720px){ .config-input{flex-direction:column;align-items:flex-start} input[type=text]{width:100%} }
</style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>📦 SISTEM INVENTORY BOOTH</h1>
      <div style="opacity:.9">Terhubung ke Google Spreadsheet (Simpan semua user ke Drive kamu)</div>
    </div>

    <!-- konfigurasi -->
    <div class="config-section">
      <h3>⚙️ Konfigurasi Google Sheets</h3>
      <div class="config-input" style="margin-bottom:10px">
        <label>URL Google Apps Script:</label>
        <!-- ------------------ MASUKKAN URL /exec DI value ------------------ -->
        <input id="apiUrl" type="text" placeholder="Masukkan URL Web App (/exec) di sini"
          value="https://script.google.com/macros/s/AKfycbzkCElaoI9fxJaIptSjgddlbs73dPtq6Mc8TMta86dts0rpf8noAYW1Cy82fn3nklcxag/exec">
      </div>

      <div style="display:flex;gap:10px;flex-wrap:wrap">
        <button class="btn-primary" onclick="saveConfig()">💾 Simpan Konfigurasi</button>
        <button class="btn-success" onclick="loadData()">🔄 Muat Data dari Sheets</button>
      </div>
      <div style="margin-top:8px;font-size:13px;color:#444">
        Pastikan Apps Script sudah <strong>deploy sebagai Web App</strong> (Execute as: <em>Me</em>, Who has access: <em>Anyone</em>)
      </div>
    </div>

    <!-- toolbar -->
    <div class="toolbar">
      <div style="flex:1">
        <button class="btn-success" onclick="openModalTambah()">➕ Tambah Produk</button>
        <button class="btn-info" onclick="hitungOtomatis()">🔄 Hitung Otomatis</button>
        <button class="btn-warning" onclick="simpanKeSheets()">💾 Simpan ke Sheets</button>
        <button class="btn-primary" onclick="exportExcel()">📊 Export Excel</button>
        <button class="btn-danger" onclick="downloadExcel()">⬇️ Download Excel</button>
      </div>
    </div>

    <!-- summary cards -->
    <div class="summary-cards" id="summaryCards">
      <div class="card"><div style="opacity:.9">Total Item</div><div id="totalItem" style="font-size:20px">0</div></div>
      <div class="card"><div style="opacity:.9">Stok Awal</div><div id="stokAwal" style="font-size:20px">0</div></div>
      <div class="card"><div style="opacity:.9">Terjual</div><div id="totalTerjual" style="font-size:20px">0</div></div>
      <div class="card"><div style="opacity:.9">Kembali</div><div id="totalKembali" style="font-size:20px">0</div></div>
      <div class="card"><div style="opacity:.9">Tersedia</div><div id="stokTersedia" style="font-size:20px">0</div></div>
    </div>

    <!-- tabel -->
    <div style="padding:18px">
      <div style="overflow:auto">
        <table id="inventoryTable">
          <thead>
            <tr>
              <th style="width:50px">No</th><th>Kode Barang</th><th>Nama Barang</th><th style="width:110px">Stok Awal</th>
              <th style="width:110px">Terjual</th><th style="width:110px">Kembali</th><th style="width:110px">Tersedia</th>
              <th style="width:90px">Selisih</th><th style="width:120px">Status</th><th style="width:90px">Aksi</th>
            </tr>
          </thead>
          <tbody id="tableBody">
            <tr><td colspan="10" class="loading">📭 Belum ada data. Klik "Muat Data dari Sheets".</td></tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

  <!-- modal tambah produk -->
  <div id="modalTambah" class="modal">
    <div class="modal-box">
      <h3>➕ Tambah Produk</h3>
      <div class="form-group">
        <label>Kode Barang</label>
        <input id="inputKode" type="text" placeholder="Contoh: CUL-001" style="width:100%;padding:8px;border-radius:6px;border:1px solid #ddd" />
      </div>
      <div class="form-group">
        <label>Nama Barang</label>
        <input id="inputNama" type="text" placeholder="Nama barang" style="width:100%;padding:8px;border-radius:6px;border:1px solid #ddd" />
      </div>
      <div class="form-group">
        <label>Stok Awal</label>
        <input id="inputStok" type="number" min="0" value="0" style="width:120px;padding:8px;border-radius:6px;border:1px solid #ddd" />
      </div>
      <div style="display:flex;gap:10px;justify-content:flex-end">
        <button class="btn-success" onclick="tambahProduk()">✅ Simpan</button>
        <button class="btn-danger" onclick="closeModal()">❌ Batal</button>
      </div>
    </div>
  </div>

<script>
/* ========== STATE ========== */
let produkData = [];           // array produk yang ditampilkan dan dikirim ke Sheets
let apiUrlConfig = "";         // URL Web App (/exec)

/* ========== INIT ========== */
window.addEventListener('load', () => {
  const saved = localStorage.getItem('apiUrl');
  if (saved) {
    document.getElementById('apiUrl').value = saved;
    apiUrlConfig = saved;
    // jangan auto-load kalau kamu ingin manual; tapi kalau mau auto: uncomment
    // loadData();
  }
});

/* ========== CONFIG ========== */
function saveConfig() {
  const url = document.getElementById('apiUrl').value.trim();
  if (!url) return alert('❌ URL tidak boleh kosong!');
  apiUrlConfig = url;
  localStorage.setItem('apiUrl', url);
  alert('✅ Konfigurasi tersimpan. Klik "Muat Data dari Sheets" untuk memuat.');
}

/* ========== LOAD DATA DARI APPS SCRIPT ========== */
async function loadData() {
  if (!apiUrlConfig) return alert('❌ Isi URL Google Apps Script dulu!');
  try {
    document.getElementById('tableBody').innerHTML = '<tr><td colspan="10" class="loading">Memuat data...</td></tr>';
    const res = await fetch(apiUrlConfig + '?action=getProduk', { method: 'GET', cache: 'no-store' });
    const text = await res.text();
    // kadang respon bukan JSON valid -> parse aman
    const result = JSON.parse(text);
    if (result.status === 'success') {
      produkData = result.data || [];
      // pastikan setiap item mempunyai field stok (number)
      produkData = produkData.map((p, i) => ({ no: p.no || i+1, kode: p.kode||'', nama: p.nama||'', stok: Number(p.stok||0), sampel: Number(p.sampel||0) }));
      renderTable();
      hitungOtomatis();
      alert('✅ Data berhasil dimuat dari Google Sheets!');
    } else {
      throw new Error(result.message || 'Response tidak sukses');
    }
  } catch (err) {
    console.error(err);
    alert('❌ Gagal muat data: ' + (err.message || err));
    renderTableEmpty();
  }
}

/* ========== SIMPAN KE SHEETS (POST ke Apps Script) ========== */
async function simpanKeSheets() {
  if (!apiUrlConfig) return alert('❌ URL Google Apps Script belum diisi!');
  try {
    // kirim data produk (produkData harus berisi kode,nama,stok,sampel)
    const res = await fetch(apiUrlConfig + '?action=saveProduk', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(produkData)
    });
    const text = await res.text();
    const result = JSON.parse(text);
    if (result.status === 'success') {
      alert('✅ Data berhasil disimpan ke Google Sheets!');
    } else {
      throw new Error(result.message || 'Gagal simpan (response error)');
    }
  } catch (err) {
    console.error(err);
    alert('❌ Gagal simpan: ' + (err.message || err));
  }
}

/* ========== RENDER TABEL ========== */
function renderTableEmpty() {
  const tbody = document.getElementById('tableBody');
  tbody.innerHTML = '<tr><td colspan="10" style="text-align:center;padding:30px">📭 Belum ada data. Klik "Muat Data dari Sheets" atau tambahkan produk.</td></tr>';
  updateSummary();
}
function renderTable() {
  const tbody = document.getElementById('tableBody');
  tbody.innerHTML = '';
  if (!produkData || produkData.length === 0) { renderTableEmpty(); return; }
  produkData.forEach((item, i) => {
    const row = tbody.insertRow();
    row.innerHTML = `
      <td>${i+1}</td>
      <td><strong>${escapeHtml(item.kode)}</strong></td>
      <td>${escapeHtml(item.nama)}</td>
      <td style="text-align:center">${item.stok}</td>
      <td style="text-align:center"><input type="number" min="0" value="0" id="terjual_${i}" onchange="hitung(${i})"></td>
      <td style="text-align:center"><input type="number" min="0" value="0" id="kembali_${i}" onchange="hitung(${i})"></td>
      <td style="text-align:center" id="tersedia_${i}">-</td>
      <td style="text-align:center" id="selisih_${i}">-</td>
      <td id="status_${i}">-</td>
      <td style="text-align:center"><button class="btn-danger" onclick="hapusProduk(${i})">🗑️</button></td>
    `;
  });
  updateSummary();
}

/* ========== PERHITUNGAN ========== */
function hitung(i) {
  const item = produkData[i];
  const terjual = parseInt(document.getElementById(`terjual_${i}`).value) || 0;
  const kembali = parseInt(document.getElementById(`kembali_${i}`).value) || 0;
  const tersedia = item.stok - terjual;
  const selisih = tersedia - kembali;

  document.getElementById(`tersedia_${i}`).textContent = tersedia;
  document.getElementById(`selisih_${i}`).textContent = selisih;

  let statusHtml = '<span class="badge badge-success">✅ Sesuai</span>';
  if (selisih > 0) statusHtml = `<span class="badge badge-danger">⚠️ Hilang ${selisih}</span>`;
  if (selisih < 0) statusHtml = `<span class="badge badge-warning">⚠️ Lebih ${Math.abs(selisih)}</span>`;
  document.getElementById(`status_${i}`).innerHTML = statusHtml;

  updateSummary();
}
function hitungOtomatis() { produkData.forEach((_,i)=>hitung(i)); updateSummary(); }

/* ========== SUMMARY ========== */
function updateSummary(){
  let totalStok=0, totalTerjual=0, totalKembali=0, totalTersedia=0;
  produkData.forEach((item,i)=>{
    totalStok += Number(item.stok||0);
    const t = Number(document.getElementById(`terjual_${i}`)?.value || 0);
    const k = Number(document.getElementById(`kembali_${i}`)?.value || 0);
    totalTerjual += t;
    totalKembali += k;
    totalTersedia += (item.stok - t);
  });
  document.getElementById('totalItem').textContent = produkData.length;
  document.getElementById('stokAwal').textContent = totalStok;
  document.getElementById('totalTerjual').textContent = totalTerjual;
  document.getElementById('totalKembali').textContent = totalKembali;
  document.getElementById('stokTersedia').textContent = totalTersedia;
}

/* ========== TAMBAH / HAPUS PRODUK (client-side) ========== */
function openModalTambah(){ document.getElementById('modalTambah').classList.add('active'); document.getElementById('modalTambah').style.display='flex'; }
function closeModal(){ document.getElementById('modalTambah').classList.remove('active'); document.getElementById('modalTambah').style.display='none'; }
function tambahProduk(){
  const kode = document.getElementById('inputKode').value.trim();
  const nama = document.getElementById('inputNama').value.trim();
  const stok = Number(document.getElementById('inputStok').value || 0);
  if (!kode || !nama) return alert('❌ Kode & nama wajib diisi!');
  produkData.push({ kode, nama, stok, sampel:0 });
  renderTable();
  closeModal();
  alert('✅ Produk ditambahkan (ingat klik Simpan ke Sheets untuk menuliskannya).');
}
function hapusProduk(i){
  if (!confirm('Yakin ingin menghapus produk?')) return;
  produkData.splice(i,1);
  renderTable();
}

/* ========== EXPORT / DOWNLOAD ========== */
function exportExcel() {
  const ws = XLSX.utils.json_to_sheet(produkData);
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'Inventory');
  XLSX.writeFile(wb, `InventoryBooth_${new Date().toISOString().slice(0,10)}.xlsx`);
}
function downloadExcel() {
  // sama dengan exportExcel (download file ke user)
  exportExcel();
}

/* ========== Utility: escape html to avoid injection in table */ 
function escapeHtml(str){ return String(str||'').replace(/[&<>"']/g, s => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'})[s]); }

/* ========== OPTIONAL: SIMPAN ke Drive via Apps Script langsung (already handled by simpanKeSheets) ========== */
/* If you want direct Drive upload without Apps Script, you need OAuth & Drive API - more complex. */

/* ========== END SCRIPT ========== */
</script>
</body>
</html>
