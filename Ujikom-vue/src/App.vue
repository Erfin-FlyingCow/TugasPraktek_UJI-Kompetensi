<template>
  <!-- Container utama untuk seluruh halaman -->
  <div class="container">
    <!-- Bagian header website -->
    <header>
      <h1>Sistem Pendaftaran Beasiswa Online</h1>
      <p>Kampus Online</p>
    </header>

    <!-- Layout utama berisi 3 kolom: panduan, form, dan hasil -->
    <main class="main-layout">
      <!-- BAGIAN KIRI: PANDUAN BEASISWA -->
      <section class="card left-panel">
        <h2>Panduan Beasiswa</h2>

        <!-- Informasi beasiswa akademik -->
        <div class="beasiswa-box">
          <h3>1. Beasiswa Akademik</h3>
          <p>
            Beasiswa akademik diberikan kepada mahasiswa yang memiliki prestasi akademik dengan IPK
            minimal 3.00.
          </p>

          <!-- Syarat beasiswa akademik -->
          <ul>
            <li>IPK minimal 3.00</li>
            <li>Mahasiswa aktif semester 1 sampai 8</li>
            <li>Mengupload berkas persyaratan</li>
          </ul>
        </div>

        <!-- Informasi beasiswa non akademik -->
        <div class="beasiswa-box">
          <h3>2. Beasiswa Non Akademik</h3>
          <p>
            Beasiswa non akademik diberikan kepada mahasiswa yang memiliki prestasi di bidang
            olahraga, seni, organisasi, atau kegiatan lainnya.
          </p>

          <!-- Syarat beasiswa non akademik -->
          <ul>
            <li>IPK minimal 3.00</li>
            <li>Mahasiswa aktif semester 1 sampai 8</li>
            <li>Mengupload bukti prestasi atau sertifikat</li>
          </ul>
        </div>

        <!-- Informasi ketentuan umum pendaftaran -->
        <div class="info-box">
          <h3>Ketentuan</h3>
          <p>
            Mahasiswa hanya dapat mendaftar beasiswa apabila IPK minimal 3.00. Jika IPK kurang dari
            3.00, pilihan beasiswa, upload berkas, dan tombol daftar akan dinonaktifkan.
          </p>
        </div>
      </section>

      <!-- BAGIAN TENGAH: FORM PENDAFTARAN -->
      <section class="card center-panel">
        <h2>Form Pendaftaran</h2>

        <!-- Form pendaftaran, saat submit akan menjalankan fungsi daftarBeasiswa -->
        <form @submit.prevent="daftarBeasiswa">
          <!-- Input nama -->
          <div class="form-group">
            <label>Nama</label>
            <input type="text" v-model="form.nama" placeholder="Masukkan nama" required />
          </div>

          <!-- Input email dengan validasi format email -->
          <div class="form-group">
            <label>Email</label>
            <input type="email" v-model="form.email" placeholder="Masukkan email" required />

            <!-- Pesan error muncul jika email tidak sesuai format -->
            <small v-if="form.email && !emailValid" class="error"> Format email tidak valid </small>
          </div>

          <!-- Input nomor HP -->
          <div class="form-group">
            <label>Nomor HP</label>
            <input
              type="text"
              v-model="form.noHp"
              placeholder="Masukkan nomor HP"
              maxlength="13"
              @input="hanyaAngka"
              required
            />
          </div>

          <!-- Pilihan semester 1 sampai 8 -->
          <div class="form-group">
            <label>Semester Saat Ini</label>
            <select v-model="form.semester" required>
              <option value="">Pilih Semester</option>

              <!-- Perulangan untuk membuat pilihan semester 1 sampai 8 -->
              <option v-for="semester in 8" :key="semester" :value="semester">
                Semester {{ semester }}
              </option>
            </select>
          </div>

          <!-- Toggle simulasi IPK -->
          <div class="form-group">
            <label>Simulasi Nilai IPK</label>

            <div class="toggle-ipk">
              <!-- Checkbox untuk mengganti nilai IPK -->
              <label class="switch">
                <input type="checkbox" v-model="ipkMemenuhiSyarat" />
                <span class="slider"></span>
              </label>

              <!-- Teks berubah sesuai kondisi IPK -->
              <span class="toggle-text">
                {{ ipkMemenuhiSyarat ? 'IPK Memenuhi Syarat' : 'IPK Tidak Memenuhi Syarat' }}
              </span>
            </div>
          </div>

          <!-- Menampilkan nilai IPK otomatis -->
          <div class="form-group">
            <label>IPK</label>
            <input type="text" :value="ipk" readonly />
          </div>

          <!-- Pesan jika IPK kurang dari 3 -->
          <div v-if="ipk < 3" class="warning">IPK tidak memenuhi syarat.</div>

          <!-- Pesan jika IPK minimal 3 -->
          <div v-if="ipk >= 3" class="success">IPK memenuhi syarat.</div>

          <!-- Pilihan jenis beasiswa -->
          <div class="form-group">
            <label>Pilihan Beasiswa</label>
            <select ref="pilihanBeasiswa" v-model="form.beasiswa" :disabled="ipk < 3" required>
              <option value="">Pilih Beasiswa</option>
              <option value="Beasiswa Akademik">Beasiswa Akademik</option>
              <option value="Beasiswa Non Akademik">Beasiswa Non Akademik</option>
            </select>
          </div>

          <!-- Upload berkas syarat -->
          <div class="form-group">
            <label>Upload Berkas Syarat</label>
            <input
              type="file"
              @change="ambilFile"
              :disabled="ipk < 3"
              accept=".pdf,.jpg,.jpeg,.png,.zip"
              required
            />
          </div>

          <!-- Tombol daftar akan mati jika IPK kurang dari 3 atau email tidak valid -->
          <button type="submit" class="btn-submit" :disabled="ipk < 3 || !emailValid">
            Daftar
          </button>
        </form>
      </section>

      <!-- BAGIAN KANAN: HASIL PENDAFTARAN -->
      <section class="card right-panel">
        <h2>Hasil Pendaftaran</h2>

        <!-- Tabel muncul jika sudah ada data pendaftaran -->
        <div v-if="hasilPendaftaran.length > 0" class="table-wrapper">
          <table>
            <thead>
              <tr>
                <th>Nama</th>
                <th>Email</th>
                <th>No HP</th>
                <th>Semester</th>
                <th>IPK</th>
                <th>Beasiswa</th>
                <th>Berkas</th>
                <th>Status</th>
              </tr>
            </thead>

            <tbody>
              <!-- Menampilkan seluruh data hasil pendaftaran -->
              <tr v-for="(data, index) in hasilPendaftaran" :key="index">
                <td>{{ data.nama }}</td>
                <td>{{ data.email }}</td>
                <td>{{ data.noHp }}</td>
                <td>{{ data.semester }}</td>
                <td>{{ data.ipk }}</td>
                <td>{{ data.beasiswa }}</td>
                <td>{{ data.berkas }}</td>
                <td>{{ data.status_ajuan }}</td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Pesan jika belum ada data pendaftaran -->
        <p v-else class="empty-text">Belum ada data pendaftaran beasiswa.</p>

        <!-- Tombol hapus data hanya muncul jika ada data pendaftaran -->
        <button
          v-if="hasilPendaftaran.length > 0"
          type="button"
          class="btn-delete"
          @click="hapusData"
        >
          Hapus Data
        </button>
      </section>
    </main>
  </div>
</template>

<script setup>
// Import fitur utama dari Vue
import { ref, reactive, computed, nextTick, onMounted, watch } from 'vue'

// Nilai IPK jika mahasiswa memenuhi syarat
const ipkLulus = 3.4

// Nilai IPK jika mahasiswa tidak memenuhi syarat
const ipkTidakLulus = 2.9

// Toggle untuk menentukan apakah IPK memenuhi syarat atau tidak
const ipkMemenuhiSyarat = ref(true)

// Nilai IPK otomatis berdasarkan toggle
const ipk = computed(() => {
  return ipkMemenuhiSyarat.value ? ipkLulus : ipkTidakLulus
})

// Referensi ke elemen pilihan beasiswa
const pilihanBeasiswa = ref(null)

// Data form pendaftaran
const form = reactive({
  nama: '',
  email: '',
  noHp: '',
  semester: '',
  beasiswa: '',
  berkas: '',
})

// Array untuk menyimpan hasil pendaftaran
const hasilPendaftaran = ref([])

// Validasi format email
const emailValid = computed(() => {
  const polaEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return polaEmail.test(form.email)
})

// Fungsi agar input nomor HP hanya berisi angka
const hanyaAngka = () => {
  form.noHp = form.noHp.replace(/\D/g, '')
}

// Fungsi untuk mengambil nama file yang diupload
const ambilFile = (event) => {
  const file = event.target.files[0]

  if (file) {
    form.berkas = file.name
  }
}

// Fungsi untuk menyimpan data pendaftaran
const daftarBeasiswa = () => {
  // Cek apakah IPK memenuhi syarat
  if (ipk.value < 3) {
    alert('IPK Anda tidak memenuhi syarat untuk mendaftar beasiswa.')
    return
  }

  // Cek apakah email valid
  if (!emailValid.value) {
    alert('Format email tidak valid.')
    return
  }

  // Cek apakah pilihan beasiswa sudah dipilih
  if (!form.beasiswa) {
    alert('Silakan pilih jenis beasiswa.')
    return
  }

  // Cek apakah berkas sudah diupload
  if (!form.berkas) {
    alert('Silakan upload berkas terlebih dahulu.')
    return
  }

  // Membuat object data pendaftaran
  const dataPendaftaran = {
    nama: form.nama,
    email: form.email,
    noHp: form.noHp,
    semester: form.semester,
    ipk: ipk.value,
    beasiswa: form.beasiswa,
    berkas: form.berkas,
    status_ajuan: 'belum di verifikasi',
  }

  // Menambahkan data ke array hasil pendaftaran
  hasilPendaftaran.value.push(dataPendaftaran)

  // Menyimpan data ke localStorage browser
  localStorage.setItem('hasilPendaftaran', JSON.stringify(hasilPendaftaran.value))

  // Menampilkan pesan berhasil
  alert('Pendaftaran beasiswa berhasil disimpan.')

  // Mengosongkan kembali form setelah data disimpan
  form.nama = ''
  form.email = ''
  form.noHp = ''
  form.semester = ''
  form.beasiswa = ''
  form.berkas = ''

  // Mengosongkan input file secara manual
  const fileInput = document.querySelector('input[type="file"]')
  if (fileInput) {
    fileInput.value = ''
  }
}

// Fungsi untuk menghapus semua data hasil pendaftaran
const hapusData = () => {
  hasilPendaftaran.value = []
  localStorage.removeItem('hasilPendaftaran')
}

// Memantau perubahan toggle IPK
watch(ipkMemenuhiSyarat, async () => {
  // Jika IPK memenuhi syarat, fokus diarahkan ke pilihan beasiswa
  if (ipk.value >= 3) {
    await nextTick()

    if (pilihanBeasiswa.value) {
      pilihanBeasiswa.value.focus()
    }
  } else {
    // Jika IPK tidak memenuhi syarat, pilihan beasiswa dan berkas dikosongkan
    form.beasiswa = ''
    form.berkas = ''

    // Mengosongkan input file
    const fileInput = document.querySelector('input[type="file"]')
    if (fileInput) {
      fileInput.value = ''
    }
  }
})

// Kode ini dijalankan saat halaman pertama kali dibuka
onMounted(() => {
  // Mengambil data pendaftaran dari localStorage
  const dataLocal = localStorage.getItem('hasilPendaftaran')

  // Jika ada data di localStorage, tampilkan kembali ke tabel hasil
  if (dataLocal) {
    hasilPendaftaran.value = JSON.parse(dataLocal)
  }

  // Jika IPK memenuhi syarat, arahkan fokus ke pilihan beasiswa
  if (ipk.value >= 3) {
    nextTick(() => {
      if (pilihanBeasiswa.value) {
        pilihanBeasiswa.value.focus()
      }
    })
  }
})
</script>
