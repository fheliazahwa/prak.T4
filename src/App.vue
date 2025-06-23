<template>
  <div id="app" class="container">
    <div class="form-box">
      <input v-model="judul" placeholder="Masukkan judul artikel" />
      <textarea v-model="isi" placeholder="Masukkan isi artikel"></textarea>
      
      <button class="simpan-btn" @click="simpanData">
        {{ isEdit ? "Update" : "+ Simpan" }}
      </button>
      <button class="load-btn" @click="loadData">🔄 Load Data</button>

      <div v-if="artikelList.length" class="output">
        <h3>Daftar Artikel:</h3>
        <ul>
          <li v-for="(artikel, index) in artikelList" :key="index">
            <strong>{{ artikel.judul }}</strong><br />
            <p>{{ artikel.isi }}</p>
            <button class="edit-btn" @click="editArtikel(index)">✏️ Edit</button>
            <button class="hapus-btn" @click="hapusArtikel(index)">🗑️ Hapus</button>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      judul: '',
      isi: '',
      artikelList: [],
      isEdit: false,
      editIndex: null
    };
  },
  mounted() {
    this.loadData(); // otomatis load saat halaman dibuka
  },
  methods: {
    simpanData() {
      if (!this.judul.trim() || !this.isi.trim()) {
        alert('Mohon isi semua kolom!');
        return;
      }

      const artikelBaru = {
        judul: this.judul,
        isi: this.isi
      };

      if (this.isEdit) {
        // Update data
        this.artikelList[this.editIndex] = artikelBaru;
        this.isEdit = false;
        this.editIndex = null;
      } else {
        // Tambah data baru
        this.artikelList.push(artikelBaru);
      }

      this.simpanKeStorage();
      this.judul = '';
      this.isi = '';
    },
    loadData() {
      const data = localStorage.getItem('artikelList');
      if (data) {
        this.artikelList = JSON.parse(data);
      }
    },
    simpanKeStorage() {
      localStorage.setItem('artikelList', JSON.stringify(this.artikelList));
    },
    hapusArtikel(index) {
      if (confirm('Yakin ingin menghapus artikel ini?')) {
        this.artikelList.splice(index, 1);
        this.simpanKeStorage();
      }
    },
    editArtikel(index) {
      this.judul = this.artikelList[index].judul;
      this.isi = this.artikelList[index].isi;
      this.isEdit = true;
      this.editIndex = index;
    }
  }
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: #f5f7fb;
  font-family: 'Inter', sans-serif;
}
.form-box {
  background: #fff;
  padding: 20px;
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  width: 320px;
}
input,
textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 8px;
  border: 1px solid #ccc;
  font-size: 14px;
}
button {
  width: 100%;
  padding: 10px;
  border: none;
  font-weight: bold;
  border-radius: 8px;
  margin-top: 10px;
  cursor: pointer;
}
.simpan-btn {
  background-color: #3b82f6;
  color: white;
}
.load-btn {
  background-color: #10b981;
  color: white;
}
.edit-btn {
  background-color: #f59e0b;
  color: white;
  margin-top: 5px;
  width: 49%;
}
.hapus-btn {
  background-color: #ef4444;
  color: white;
  margin-top: 5px;
  width: 49%;
  float: right;
}
.output {
  margin-top: 20px;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  background: #f1f5f9;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
}
</style>
