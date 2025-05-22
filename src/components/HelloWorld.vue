<script setup>
import { ref, onMounted } from 'vue'
import { collection, getDocs, addDoc } from 'firebase/firestore'
import { db } from '../firebaseConfig.js'

defineProps({
  msg: String,
})

const items = ref([])
const newItem = ref('')
const loading = ref(false)
const error = ref('')

const fetchItems = async () => {
  loading.value = true
  error.value = ''
  try {
    const querySnapshot = await getDocs(collection(db, 'items'))
    items.value = querySnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }))
  } catch (e) {
    error.value = 'Error al cargar los datos'
  }
  loading.value = false
}

onMounted(fetchItems)

const addItem = async () => {
  if (newItem.value.trim() === '') return
  loading.value = true
  error.value = ''
  try {
    await addDoc(collection(db, 'items'), { name: newItem.value })
    newItem.value = ''
    await fetchItems()
  } catch (e) {
    error.value = 'Error al agregar el item'
  }
  loading.value = false
}
</script>

<template>
  <div class="hello-container">
    <h1 class="main-title">{{ msg }}</h1>

    <div class="firestore-section">
      <h2>Lista de Items</h2>
      <div v-if="loading" class="loading">Cargando...</div>
      <div v-if="error" class="error">{{ error }}</div>
      <ul v-else>
        <li v-for="item in items" :key="item.id" class="item">
          <span>{{ item.name }}</span>
        </li>
      </ul>
      <div class="add-section">
        <input
          v-model="newItem"
          placeholder="Nuevo item"
          @keyup.enter="addItem"
        />
        <button @click="addItem">Agregar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.hello-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2.5rem 0;
}

.main-title {
  font-size: 2.2rem;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 2rem;
  letter-spacing: 1px;
}

.firestore-section {
  background: #fff;
  border-radius: 1.5em;
  box-shadow: 0 8px 32px 0 rgba(60, 60, 60, 0.12);
  padding: 2rem 2.5rem;
  min-width: 320px;
  max-width: 90vw;
  text-align: center;
}

.firestore-section h2 {
  color: #42b883;
  margin-bottom: 1.2rem;
  font-size: 1.3rem;
  font-weight: 600;
}

.loading {
  color: #888;
  margin-bottom: 1rem;
}

.error {
  color: #e74c3c;
  margin-bottom: 1rem;
}

ul {
  list-style: none;
  padding: 0;
  margin-bottom: 1.2rem;
}

.item {
  background: #f0fff4;
  margin: 0.4rem 0;
  padding: 0.7rem 1rem;
  border-radius: 0.7em;
  box-shadow: 0 2px 8px 0 rgba(66, 184, 131, 0.05);
  font-size: 1.05rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.add-section {
  display: flex;
  gap: 0.5rem;
  justify-content: center;
  margin-top: 1rem;
}

.add-section input {
  padding: 0.4rem 1rem;
  border-radius: 0.5em;
  border: 1px solid #d1d5db;
  font-size: 1rem;
  outline: none;
  transition: border 0.2s;
}

.add-section input:focus {
  border: 1.5px solid #42b883;
}

.add-section button {
  padding: 0.4rem 1.2rem;
  border-radius: 0.5em;
  background: linear-gradient(90deg, #42b883 60%, #38b2ac 100%);
  color: white;
  border: none;
  cursor: pointer;
  font-weight: 600;
  font-size: 1rem;
  transition: background 0.2s, transform 0.2s;
}

.add-section button:hover {
  background: linear-gradient(90deg, #368f6e 60%, #319795 100%);
  transform: scale(1.05);
}
</style>
