<template>
  <div class="wrapper">
    <form @submit.prevent="isEditing ? update() : create()" class="form">
      <input v-model="form.title" placeholder="Judul artikel" required />
      <textarea v-model="form.content" placeholder="Isi konten..." required></textarea>
      <button>{{ isEditing ? '✏️ Update' : '➕ Simpan' }}</button>
    </form>

    <ul class="list">
      <li v-for="item in articles" :key="item.id" class="card">
        <h3>{{ item.title }}</h3>
        <p>{{ item.content }}</p>
        <div class="actions">
          <button @click="edit(item)">Edit</button>
          <button @click="hapus(item.id)">Hapus</button>
        </div>
      </li>
    </ul>

    <button @click="load" class="load-btn">🔄 Load Data</button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const API = 'http://localhost:3000/articles'

const articles = ref([])
const form = ref({ title: '', content: '' })
const isEditing = ref(false)
const editingId = ref(null)

onMounted(load)

function load() {
  axios.get(API).then(res => {
    articles.value = res.data
  })
}

function create() {
  axios.post(API, form.value).then(() => {
    form.value = { title: '', content: '' }
    load()
  })
}

function edit(item) {
  form.value = { title: item.title, content: item.content }
  editingId.value = item.id
  isEditing.value = true
}

function update() {
  axios.put(`${API}/${editingId.value}`, form.value).then(() => {
    form.value = { title: '', content: '' }
    isEditing.value = false
    editingId.value = null
    load()
  })
}

function hapus(id) {
  axios.delete(`${API}/${id}`).then(load)
}
</script>

<style scoped>
.wrapper {
  width: 100%;
  max-width: 500px;
  background: #fff;
  padding: 24px;
  border-radius: 14px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.05);
}
.form {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.form input,
.form textarea {
  padding: 10px 14px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
}
.form button {
  background-color: #3498db;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
}
.list {
  list-style: none;
  padding: 0;
  margin: 20px 0;
}
.card {
  background: #f1f3f6;
  padding: 14px;
  margin-bottom: 12px;
  border-radius: 10px;
}
.card h3 {
  margin: 0;
  font-weight: 600;
}
.card p {
  margin: 5px 0 0;
}
.actions {
  margin-top: 10px;
  display: flex;
  gap: 10px;
}
.actions button {
  padding: 6px 10px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
}
.actions button:first-child {
  background-color: #f1c40f;
}
.actions button:last-child {
  background-color: #e74c3c;
  color: white;
}
.load-btn {
  margin-top: 12px;
  background-color: #2ecc71;
  color: white;
  border: none;
  padding: 10px 20px;
  font-weight: bold;
  border-radius: 8px;
  cursor: pointer;
}
</style>
