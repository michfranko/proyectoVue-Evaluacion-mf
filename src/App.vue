<script setup>
import { ref, onMounted } from 'vue'
import { collection, getDocs } from 'firebase/firestore'
import { db } from './firebaseConfig.js'

const productos = ref([])
const loading = ref(false)
const error = ref('')

const fetchProductos = async () => {
  loading.value = true
  error.value = ''
  try {
    const querySnapshot = await getDocs(collection(db, 'products'))
    productos.value = querySnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }))
  } catch (e) {
    error.value = 'Error al cargar los productos'
  }
  loading.value = false
}

onMounted(fetchProductos)
</script>

<template>
  <div class="app-container">
    <h1 class="main-title">Lista de Productos</h1>
    <div v-if="loading" class="loading">Cargando...</div>
    <div v-if="error" class="error">{{ error }}</div>
    <ul v-else>
      <li v-for="producto in productos" :key="producto.id" class="item">
        <strong>{{ producto.nombre }}</strong><br />
        Precio: ${{ producto.precio }}<br />
        Stock: {{ producto.stock }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  background: linear-gradient(135deg, #e0e7ff 0%, #f0fff4 100%);
  padding-top: 3rem;
}
.main-title {
  font-size: 2.2rem;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 2rem;
  letter-spacing: 1px;
  text-shadow: 0 2px 8px #e0e7ff80;
}
.loading {
  color: #888;
  margin-bottom: 1rem;
  font-size: 1.1rem;
}
.error {
  color: #e74c3c;
  margin-bottom: 1rem;
  font-weight: 600;
}
ul {
  list-style: none;
  padding: 0;
  margin-bottom: 1.2rem;
  width: 100%;
  max-width: 400px;
}
.item {
  background: #fff;
  margin: 0.4rem 0;
  padding: 1rem 1.2rem;
  border-radius: 0.9em;
  box-shadow: 0 2px 12px 0 rgba(66, 184, 131, 0.10);
  font-size: 1.1rem;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  transition: transform 0.15s;
}
.item:hover {
  transform: scale(1.03);
  background: #e6fffa;
}
</style>
