# Inventory-Stok-dilwali
LAPORAN INVENTORY BOOTH
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Laporan Inventory Booth</title>
<style>
  body {
    font-family: 'Segoe UI', Arial, sans-serif;
    padding: 20px;
    background: #f5f5f5;
    max-width: 1400px;
    margin: 0 auto;
  }
  h1 {
    text-align: center;
    color: #2c3e50;
    margin-bottom: 10px;
  }
  .header-info {
    text-align: center;
    margin-bottom: 20px;
    color: #34495e;
  }
  .buttons {
    text-align: center;
    margin-bottom: 20px;
  }
  button {
    padding: 10px 20px;
    margin: 0 5px;
    background: #1abc9c;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-weight: 600;
    font-size: 14px;
  }
  button:hover {
    background: #16a085;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    background: white;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    margin-bottom: 30px;
  }
  th {
    background: #34495e;
    color: white;
    padding: 12px;
    text-align: left;
    font-weight: 600;
    font-size: 13px;
    border: 1px solid #2c3e50;
  }
  td {
    padding: 10px;
    border: 1px solid #ddd;
    font-size: 13px;
  }
  tr:nth-child(even) {
    background: #f9f9f9;
  }
  .editable {
    background: #fff3cd;
  }
  input[type="number"] {
    width: 60px;
    padding: 5px;
    border: 1px solid #bdc3c7;
    border-radius: 4px;
    text-align: center;
  }
  .total-row {
    background: #ecf0f1 !important;
    font-weight: 700;
  }
  .summary {
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    margin-bottom: 20px;
  }
  .summary h3 {
    margin-top: 0;
    color: #2c3e50;
    border-bottom: 2px solid #1abc9c;
    padding-bottom: 10px;
  }
  .summary-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 15px;
    margin-top: 15px;
  }
  .summary-item {
    padding: 15px;
    background: #f8f9fa;
    border-radius: 6px;
    border-left: 4px solid #1abc9c;
  }
  .summary-label {
    font-size: 12px;
    color: #7f8c8d;
    margin-bottom: 5px;
  }
  .summary-value {
    font-size: 24px;
    font-weight: 700;
    color: #2c3e50;
  }
  .alert {
    background: #fff3cd;
    border: 2px solid #ffc107;
    padding: 15px;
    border-radius: 6px;
    margin-bottom: 20px;
  }
  .alert h4 {
    margin: 0 0 10px 0;
    color: #856404;
  }
  @media print {
    .buttons, .alert { display: none; }
    body { background: white; }
  }
</style>
</head>
<body>

<h1>LAPORAN INVENTORY BOOTH</h1>
<div class="header-info">
  <strong>Acara Booth Tanggal: 11 - 12 Oktober 2025</strong><br>
  Tim Surprice
</div>

<div class="buttons">
  <button onclick="hitungOtomatis()">🔄 Hitung Otomatis</button>
  <button onclick="window.print()">🖨️ Print / Save PDF</button>
  <button onclick="exportToExcel()">📊 Export ke Excel</button>
</div>

<div class="alert">
  <h4>📝 Cara Penggunaan:</h4>
  <ol style="margin: 5px 0; padding-left: 20px;">
    <li>Isi kolom <strong>"Terjual Hari 1"</strong> dan <strong>"Terjual Hari 2"</strong></li>
    <li>Isi kolom <strong>"Stok Kembali"</strong> (barang yang dibawa pulang)</li>
    <li>Klik tombol <strong>"Hitung Otomatis"</strong></li>
    <li>Kolom "Selisih" akan otomatis terhitung (jika ada barang hilang/rusak)</li>
  </ol>
</div>

<div class="summary">
  <h3>📊 Ringkasan Inventory</h3>
  <div class="summary-grid">
    <div class="summary-item">
      <div class="summary-label">Total Item</div>
      <div class="summary-value" id="totalItem">0</div>
    </div>
    <div class="summary-item">
      <div class="summary-label">Total Stok Keluar</div>
      <div class="summary-value" id="totalKeluar">0</div>
    </div>
    <div class="summary-item">
      <div class="summary-label">Total Terjual</div>
      <div class="summary-value" id="totalTerjual">0</div>
    </div>
    <div class="summary-item">
      <div class="summary-label">Total Kembali</div>
      <div class="summary-value" id="totalKembali">0</div>
    </div>
    <div class="summary-item" style="border-left-color: #e74c3c;">
      <div class="summary-label">Total Selisih</div>
      <div class="summary-value" id="totalSelisih" style="color: #e74c3c;">0</div>
    </div>
  </div>
</div>

<table id="inventoryTable">
  <thead>
    <tr>
      <th style="width: 40px;">No</th>
      <th style="width: 150px;">Kode Barang</th>
      <th>Nama Barang</th>
      <th style="width: 80px;">Stok Keluar</th>
      <th style="width: 90px;" class="editable">Terjual Hari 1</th>
      <th style="width: 90px;" class="editable">Terjual Hari 2</th>
      <th style="width: 90px;">Total Terjual</th>
      <th style="width: 90px;" class="editable">Stok Kembali</th>
      <th style="width: 80px;">Selisih</th>
      <th style="width: 120px;">Keterangan</th>
    </tr>
  </thead>
  <tbody id="tableBody">
  </tbody>
</table>

<script>
const data = [
  {no: 1, kode: "Cul 3805", nama: "Cul 3805", stok: 10, sampel: 1},
  {no: 2, kode: "TLG TESSA-1", nama: "TLG TESSA-1", stok: 10, sampel: 1},
  {no: 3, kode: "GILI 9604-GGI", nama: "GILI 9604 - GGI (GBT)", stok: 1, sampel: 1},
  {no: 4, kode: "GK", nama: "GK", stok: 3},
  {no: 5, kode: "GB", nama: "GB", stok: 1},
  {no: 6, kode: "Cul 3608", nama: "Cul 3608", stok: 5, sampel: 1},
  {no: 7, kode: "2Em 3508/11", nama: "2Em 3508/11", stok: 1, sampel: 1},
  {no: 8, kode: "TLG Cul3604", nama: "TLG - Cul3604 / Cui 1005", stok: 5, sampel: 1},
  {no: 9, kode: "TLG Cul 3607", nama: "TLG - Cul 3607 / Cui 1010.5", stok: 5, sampel: 1},
  {no: 10, kode: "TLG Cul 3612", nama: "TLG Cul 3612 M/L", stok: 5, sampel: 1},
  {no: 11, kode: "TLG TANIA-1", nama: "TLG - TANIA - 1", stok: 5, sampel: 1},
  {no: 12, kode: "OEM3512", nama: "OEM3512 s/m /L", stok: 6, sampel: 1},
  {no: 13, kode: "ATZ 9604-P", nama: "ATZ 9604 - P", stok: 2},
  {no: 14, kode: "ATZ 9604-B", nama: "ATZ 9604 - B", stok: 1},
  {no: 15, kode: "ATZ 9604-U", nama: "ATZ 9604 - U", stok: 2, sampel: 1},
  {no: 16, kode: "OEM 8505/06", nama: "OEM 8505 /06", stok: 85, sampel: 1},
  {no: 17, kode: "MND 1002-G", nama: "MND 1002 - G", stok: 85, sampel: 1},
  {no: 18, kode: "Cui 1005-L", nama: "Cui 1005 - L", stok: 5, sampel: 1},
  {no: 19, kode: "Cui 1005-M", nama: "Cui 1005 - M", stok: 5, sampel: 1},
  {no: 20, kode: "TFB 3602", nama: "TFB 3602", stok: 5},
  {no: 21, kode: "TFB 3603", nama: "TFB 3603", stok: 3},
  {no: 22, kode: "TFB 3604", nama: "TFB 3604", stok: 5, sampel: 1},
  {no: 23, kode: "TKB 3605", nama: "TKB 3605", stok: 3},
  {no: 24, kode: "TKB 3606", nama: "TKB 3606", stok: 3},
  {no: 25, kode: "TKB 3607", nama: "TKB 3607", stok: 3},
  {no: 26, kode: "TKB 3608", nama: "TKB 3608", stok: 3},
  {no: 27, kode: "TKB 3609", nama: "TKB 3609", stok: 3, sampel: 1},
  {no: 28, kode: "TLG Fei 5602", nama: "TLG Fei 5602 m/L", stok: 3},
  {no: 29, kode: "Fei B602", nama: "Fei B602 m/E", stok: 3, sampel: 1},
  {no: 30, kode: "Fei 5602 L", nama: "Fei 5602 L", stok: 3, sampel: 1},
  {no: 31, kode: "TKB 3610", nama: "TKB 3610", stok: 3, sampel: 1},
  {no: 32, kode: "TLG Fei 5601", nama: "TLG Fei 5601 m/L", stok: 3},
  {no: 33, kode: "Fei 5601 m", nama: "Fei 5601 m", stok: 3, sampel: 1},
  {no: 34, kode: "Fei 5601 L", nama: "Fei 5601 L", stok: 3, sampel: 1},
  {no: 35, kode: "Fei Si 02 m", nama: "Fei Si 02 m", stok: 3, sampel: 1},
  {no: 36, kode: "Fei 5103 m/L", nama: "Fei 5103 m/L", stok: 3},
  {no: 37, kode: "Fei 5103 m", nama: "Fei 5103 m", stok: 3, sampel: 1},
  {no: 38, kode: "Fei 5103 L", nama: "Fei 5103 L", stok: 3, sampel: 1},
  {no: 39, kode: "Fei 5103 4 m", nama: "Fei 5103 4 m", stok: 3, sampel: 1},
  {no: 40, kode: "SGB 2602", nama: "SGB 2602", stok: 2, sampel: 1},
  {no: 41, kode: "SGB 3604", nama: "SGB 3604", stok: 2, sampel: 1},
  {no: 42, kode: "SGB 3605", nama: "SGB 3605", stok: 1, sampel: 1},
  {no: 43, kode: "KFC 5603", nama: "KFC 5603", stok: 3},
  {no: 44, kode: "PTT 5601", nama: "PTT 5601", stok: 3},
  {no: 45, kode: "PTT 5603", nama: "PTT 5603", stok: 3},
  {no: 46, kode: "KFC Sar2", nama: "KFC Sar2", stok: 2},
  {no: 47, kode: "PTT 5611", nama: "PTT 5611", stok: 3},
  {no: 48, kode: "PTT 5612", nama: "PTT 5612", stok: 3},
  {no: 49, kode: "PTT 5615", nama: "PTT 5615", stok: 3},
  {no: 50, kode: "QYE 5705", nama: "QYE 5705", stok: 3},
  {no: 51, kode: "QYE 5706", nama: "QYE 5706", stok: 3, sampel: 1},
  {no: 52, kode: "QYE 5701", nama: "QYE 5701", stok: 3},
  {no: 53, kode: "QYE 5702", nama: "QYE 5702", stok: 3},
  {no: 54, kode: "QYE 5703", nama: "QYE 5703", stok: 3},
  {no: 55, kode: "QYE 5704", nama: "QYE 5704", stok: 3, sampel: 1}
];

function renderTable() {
  const tbody = document.getElementById('tableBody');
  tbody.innerHTML = '';
  
  data.forEach((item, index) => {
    const row = tbody.insertRow();
    row.innerHTML = `
      <td>${item.no}</td>
      <td>${item.kode}</td>
      <td>${item.nama}</td>
      <td style="text-align: center;">${item.stok}</td>
      <td style="text-align: center;"><input type="number" min="0" value="0" id="h1_${index}" onchange="hitung(${index})"></td>
      <td style="text-align: center;"><input type="number" min="0" value="0" id="h2_${index}" onchange="hitung(${index})"></td>
      <td style="text-align: center;" id="total_${index}">0</td>
      <td style="text-align: center;"><input type="number" min="0" value="0" id="kembali_${index}" onchange="hitung(${index})"></td>
      <td style="text-align: center;" id="selisih_${index}">0</td>
      <td><span id="ket_${index}">${item.sampel ? 'Sampel: ' + item.sampel : ''}</span></td>
    `;
  });
  
  // Total row
  const totalRow = tbody.insertRow();
  totalRow.className = 'total-row';
  totalRow.innerHTML = `
    <td colspan="3" style="text-align: right; font-weight: bold;">TOTAL:</td>
    <td style="text-align: center;" id="footerStok">0</td>
    <td style="text-align: center;" id="footerH1">0</td>
    <td style="text-align: center;" id="footerH2">0</td>
    <td style="text-align: center;" id="footerTotal">0</td>
    <td style="text-align: center;" id="footerKembali">0</td>
    <td style="text-align: center;" id="footerSelisih">0</td>
    <td></td>
  `;
  
  updateSummary();
}

function hitung(index) {
  const h1 = parseInt(document.getElementById(`h1_${index}`).value) || 0;
  const h2 = parseInt(document.getElementById(`h2_${index}`).value) || 0;
  const kembali = parseInt(document.getElementById(`kembali_${index}`).value) || 0;
  const stok = data[index].stok;
  
  const totalTerjual = h1 + h2;
  const selisih = stok - totalTerjual - kembali;
  
  document.getElementById(`total_${index}`).textContent = totalTerjual;
  document.getElementById(`selisih_${index}`).textContent = selisih;
  
  const ketEl = document.getElementById(`ket_${index}`);
  let ket = data[index].sampel ? `Sampel: ${data[index].sampel}` : '';
  if (selisih > 0) {
    ket += (ket ? ' | ' : '') + `⚠️ Hilang/Rusak: ${selisih}`;
    document.getElementById(`selisih_${index}`).style.color = '#e74c3c';
    document.getElementById(`selisih_${index}`).style.fontWeight = 'bold';
  } else if (selisih < 0) {
    ket += (ket ? ' | ' : '') + `⚠️ Lebih: ${Math.abs(selisih)}`;
    document.getElementById(`selisih_${index}`).style.color = '#e67e22';
    document.getElementById(`selisih_${index}`).style.fontWeight = 'bold';
  } else {
    document.getElementById(`selisih_${index}`).style.color = '#27ae60';
    document.getElementById(`selisih_${index}`).style.fontWeight = 'bold';
  }
  ketEl.innerHTML = ket;
}

function hitungOtomatis() {
  let totalStok = 0;
  let totalH1 = 0;
  let totalH2 = 0;
  let totalTerjual = 0;
  let totalKembali = 0;
  let totalSelisih = 0;
  
  data.forEach((item, index) => {
    hitung(index);
    totalStok += item.stok;
    totalH1 += parseInt(document.getElementById(`h1_${index}`).value) || 0;
    totalH2 += parseInt(document.getElementById(`h2_${index}`).value) || 0;
    totalTerjual += parseInt(document.getElementById(`total_${index}`).textContent);
    totalKembali += parseInt(document.getElementById(`kembali_${index}`).value) || 0;
    totalSelisih += parseInt(document.getElementById(`selisih_${index}`).textContent);
  });
  
  document.getElementById('footerStok').textContent = totalStok;
  document.getElementById('footerH1').textContent = totalH1;
  document.getElementById('footerH2').textContent = totalH2;
  document.getElementById('footerTotal').textContent = totalTerjual;
  document.getElementById('footerKembali').textContent = totalKembali;
  document.getElementById('footerSelisih').textContent = totalSelisih;
  
  updateSummary();
}

function updateSummary() {
  document.getElementById('totalItem').textContent = data.length;
  document.getElementById('totalKeluar').textContent = document.getElementById('footerStok').textContent;
  document.getElementById('totalTerjual').textContent = document.getElementById('footerTotal').textContent;
  document.getElementById('totalKembali').textContent = document.getElementById('footerKembali').textContent;
  document.getElementById('totalSelisih').textContent = document.getElementById('footerSelisih').textContent;
}

function exportToExcel() {
  alert('Untuk export ke Excel:\n1. Klik tombol "Print / Save PDF"\n2. Pilih "Save as PDF"\n3. Buka PDF dan copy ke Excel\n\nAtau screenshot tabel ini dan paste ke Excel.');
}

renderTable();
</script>

</body>
</html>
