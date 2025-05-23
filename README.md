<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pencatatan Kilometer Mobil</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
  * {
    box-sizing: border-box;
  }
  body {
    font-family: 'Poppins', sans-serif;
    background: #eef2f7;
    margin: 0;
    padding: 20px;
    color: #2c3e50;
  }
  .container {
    max-width: 900px;
    margin: 0 auto;
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.1);
    padding: 30px 40px;
  }
  h1 {
    text-align: center;
    margin-bottom: 32px;
    font-weight: 700;
    font-size: 2rem;
    color: #34495e;
  }
  form {
    display: flex;
    flex-direction: column;
    gap: 20px;
  }
  /* Group each row of two inputs side-by-side */
  .form-row {
    display: flex;
    gap: 20px;
  }
  /* Each input group takes equal space */
  .form-group {
    flex: 1;
    display: flex;
    flex-direction: column;
  }
  label {
    font-weight: 600;
    margin-bottom: 8px;
    color: #34495e;
  }
  input[type="date"],
  input[type="time"],
  input[type="number"],
  input[type="text"] {
    padding: 10px 14px;
    font-size: 1rem;
    border: 2px solid #cbd5e1;
    border-radius: 8px;
    transition: border-color 0.3s ease-in-out;
  }
  input[type="date"]:focus,
  input[type="time"]:focus,
  input[type="number"]:focus,
  input[type="text"]:focus {
    outline: none;
    border-color: #4a90e2;
    box-shadow: 0 0 8px #4a90e2aa;
  }
  button {
    margin-top: 14px;
    background-color: #4a90e2;
    border: none;
    color: white;
    padding: 14px 0;
    font-weight: 700;
    font-size: 1.1rem;
    border-radius: 9px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    align-self: flex-start;
    padding-left: 40px;
    padding-right: 40px;
  }
  button:hover {
    background-color: #357abd;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 30px;
    font-size: 0.95rem;
  }
  thead {
    background-color: #4a90e2;
    color: white;
  }
  th, td {
    padding: 12px 15px;
    border: 1px solid #d1d5db;
    text-align: center;
  }
  tbody tr:nth-child(even) {
    background-color: #f1f5f9;
  }
  tbody tr:hover {
    background-color: #e0e7ff;
  }
  .btn-clear {
    margin-top: 15px;
    background-color: #e53e3e;
    border: none;
    color: white;
    padding: 12px 0;
    width: 150px;
    font-weight: 700;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  .btn-clear:hover {
    background-color: #9b2c2c;
  }
  @media (max-width: 700px) {
    .form-row {
      flex-direction: column;
    }
    button {
      align-self: stretch;
      width: 100%;
      padding-left: 0;
      padding-right: 0;
    }
  }
</style>
</head>
<body>
  <div class="container">
    <h1>Pencatatan Kilometer Mobil PT. Unggul Teknos</h1>
    <form id="kmForm" autocomplete="off">
      <div class="form-row">
        <div class="form-group">
          <label for="tanggal">Tanggal</label>
          <input type="date" id="tanggal" name="tanggal" required />
        </div>
        <div class="form-group">
          <label for="platNomor">Plat Nomor Mobil</label>
          <input type="text" id="platNomor" name="platNomor" placeholder="Misal: B 1234 CD" required />
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="jamKeluar">Jam Keluar</label>
          <input type="time" id="jamKeluar" name="jamKeluar" required />
        </div>
        <div class="form-group">
          <label for="jamMasuk">Jam Masuk</label>
          <input type="time" id="jamMasuk" name="jamMasuk" required />
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="kmAwal">Kilometer Awal (km)</label>
          <input type="number" id="kmAwal" name="kmAwal" min="0" required placeholder="Misal: 15000" />
        </div>
        <div class="form-group">
          <label for="kmAkhir">Kilometer Akhir (km)</label>
          <input type="number" id="kmAkhir" name="kmAkhir" min="0" required placeholder="Misal: 15200" />
        </div>
      </div>
      <button type="submit">Simpan Data</button>
    </form>

    <table id="recordsTable">
      <thead>
        <tr>
          <th>Tanggal</th>
          <th>Plat Nomor</th>
          <th>Jam Keluar</th>
          <th>Jam Masuk</th>
          <th>Kilometer Awal</th>
          <th>Kilometer Akhir</th>
          <th>Jarak Tempuh (km)</th>
        </tr>
      </thead>
      <tbody>
        <!-- Records inserted here -->
      </tbody>
    </table>

    <button class="btn-clear" id="clearBtn">Hapus Semua Data</button>
  </div>

<script>
  const kmForm = document.getElementById('kmForm');
  const recordsTableBody = document.querySelector('#recordsTable tbody');
  const clearBtn = document.getElementById('clearBtn');

  function loadRecords() {
    return JSON.parse(localStorage.getItem('mobilKmRecords')) || [];
  }

  function saveRecords(records) {
    localStorage.setItem('mobilKmRecords', JSON.stringify(records));
  }

  function timeToMinutes(t) {
    const [h, m] = t.split(':').map(Number);
    return h * 60 + m;
  }

  function renderRecords() {
    const records = loadRecords();
    recordsTableBody.innerHTML = '';
    if (records.length === 0) {
      const tr = document.createElement('tr');
      tr.innerHTML = '<td colspan="7" style="color:#888; text-align:center;">Belum ada data pencatatan</td>';
      recordsTableBody.appendChild(tr);
      return;
    }
    records.forEach(record => {
      const jarak = record.kmAkhir - record.kmAwal;
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${record.tanggal}</td>
        <td>${record.platNomor}</td>
        <td>${record.jamKeluar}</td>
        <td>${record.jamMasuk}</td>
        <td>${record.kmAwal.toLocaleString('id-ID')}</td>
        <td>${record.kmAkhir.toLocaleString('id-ID')}</td>
        <td>${jarak.toLocaleString('id-ID')}</td>
      `;
      recordsTableBody.appendChild(tr);
    });
  }

  kmForm.addEventListener('submit', function(e) {
    e.preventDefault();
    const tanggal = kmForm.tanggal.value;
    const platNomor = kmForm.platNomor.value.trim();
    const jamKeluar = kmForm.jamKeluar.value;
    const jamMasuk = kmForm.jamMasuk.value;
    const kmAwal = parseInt(kmForm.kmAwal.value, 10);
    const kmAkhir = parseInt(kmForm.kmAkhir.value, 10);

    if (!tanggal || !platNomor || !jamKeluar || !jamMasuk || isNaN(kmAwal) || isNaN(kmAkhir)) {
      alert('Mohon lengkapi semua data dengan benar.');
      return;
    }

    if (kmAkhir < kmAwal) {
      alert('Kilometer Akhir harus lebih besar atau sama dengan Kilometer Awal.');
      return;
    }

    let keluarMinutes = timeToMinutes(jamKeluar);
    let masukMinutes = timeToMinutes(jamMasuk);

    if (masukMinutes < keluarMinutes) {
      masukMinutes += 24 * 60;
    }

    if (masukMinutes < keluarMinutes) {
      alert('Jam Masuk tidak boleh kurang dari Jam Keluar.');
      return;
    }

    const records = loadRecords();
    records.push({ tanggal, platNomor, jamKeluar, jamMasuk, kmAwal, kmAkhir });
    saveRecords(records);
    renderRecords();
    kmForm.reset();

    const today = new Date().toISOString().split('T')[0];
    kmForm.tanggal.value = today;
  });

  clearBtn.addEventListener('click', () => {
    if (confirm('Apakah Anda yakin ingin menghapus semua data pencatatan?')) {
      localStorage.removeItem('mobilKmRecords');
      renderRecords();
    }
  });

  window.addEventListener('load', () => {
    const today = new Date().toISOString().split('T')[0];
    kmForm.tanggal.value = today;
    renderRecords();
  });
</script>
</body>
</html>

