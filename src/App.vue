<script setup>
import { ref, onMounted } from 'vue'

const API = import.meta.env.VITE_API_URL || 'http://localhost:4000'
const CITIES = ['Guayaquil','Quito','Cuenca','Milagro','Manta','Loja']

const form = ref({
  dni: '',
  nombres: '',
  apellidos: '',
  fechaNacimiento: '',
  genero: 'F',
  ciudad: 'Guayaquil'
})

const personas = ref([])
const editId = ref(null)
const error = ref('')
const ok = ref('')

function validate(){
  if(!/^\d{10}$/.test(form.value.dni)) return 'DNI debe tener 10 dígitos'
  if(form.value.nombres.trim().length < 2) return 'Nombres mínimos 2 caracteres'
  if(form.value.apellidos.trim().length < 2) return 'Apellidos mínimos 2 caracteres'
  if(!form.value.fechaNacimiento) return 'Selecciona fecha'
  const d = new Date(form.value.fechaNacimiento)
  const min = new Date('1900-01-01'); const max = new Date()
  if(d < min || d > max) return 'Fecha fuera de rango'
  return ''
}

async function load(){
  try{
    const res = await fetch(`${API}/api/personas`)
    if(!res.ok) throw new Error('GET fail')
    personas.value = await res.json()
  }catch(e){
    error.value = 'No se pudo cargar la lista. Verifica que el backend esté corriendo.'
  }
}

async function onSubmit(e){
  e.preventDefault()
  const msg = validate()
  if(msg){ error.value = msg; ok.value=''; return }
  error.value = ''

  const method = editId.value ? 'PUT' : 'POST'
  const url = editId.value ? `${API}/api/personas/${editId.value}` : `${API}/api/personas`

  const res = await fetch(url, {
    method,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form.value)
  })
  if(!res.ok){
    const err = await res.json().catch(()=>({}))
    ok.value = ''
    error.value = err?.message || 'Error en el guardado'
    return
  }
  await load()
  form.value = { dni:'', nombres:'', apellidos:'', fechaNacimiento:'', genero:'F', ciudad:'Guayaquil' }
  editId.value = null
  ok.value = method === 'PUT' ? 'Registro actualizado' : 'Registro creado'
  setTimeout(()=> ok.value = '', 1800)
}

function edit(p){
  form.value = {
    dni: p.dni,
    nombres: p.nombres,
    apellidos: p.apellidos,
    fechaNacimiento: p.fechaNacimiento?.slice(0,10),
    genero: p.genero,
    ciudad: p.ciudad
  }
  editId.value = p._id
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

async function removeItem(id){
  if(!confirm('¿Eliminar registro?')) return
  await fetch(`${API}/api/personas/${id}`, { method: 'DELETE' })
  await load()
}

onMounted(load)
</script>

<template>
  <div class="page">
    <!-- HERO -->
    <header class="hero">
      <div class="hero-inner">
        <div class="brand">MUNDO MARVEL</div>
        <h1 class="title">FORMULARIO DE REGISTRO</h1>
        <p class="subtitle">Registrate para que estes al tanto de las noticias de tus superhéroes favoritos</p>
      </div>
      <div class="hero-shine" />
    </header>

    <!-- CONTENEDOR CENTRADO -->
    <main class="container">
      <!-- FORM -->
      <section class="card">
        <h2 class="card-title">{{ editId ? 'Editar registro' : 'Nuevo registro' }}</h2>

        <div v-if="error" class="alert error">{{ error }}</div>
        <div v-if="ok" class="alert ok">{{ ok }}</div>

        <form @submit="onSubmit" class="form-grid">
          <label>
            <span>DNI</span>
            <input name="dni" v-model="form.dni" required pattern="\d{10}" placeholder="10 dígitos" />
          </label>

          <label>
            <span>Nombres</span>
            <input name="nombres" v-model="form.nombres" required placeholder="Ej: Peter" />
          </label>

          <label>
            <span>Apellidos</span>
            <input name="apellidos" v-model="form.apellidos" required placeholder="Ej: Parker" />
          </label>

          <label>
            <span>Fecha de nacimiento</span>
            <input type="date" name="fechaNacimiento" v-model="form.fechaNacimiento" required />
          </label>

          <fieldset class="radios">
            <legend>Género</legend>
            <label class="radio"><input type="radio" value="F" v-model="form.genero"> F</label>
            <label class="radio"><input type="radio" value="M" v-model="form.genero"> M</label>
            <label class="radio"><input type="radio" value="O" v-model="form.genero"> O</label>
          </fieldset>

          <label>
            <span>Ciudad</span>
            <select name="ciudad" v-model="form.ciudad">
              <option v-for="c in ['Guayaquil','Quito','Cuenca','Milagro','Manta','Loja']" :key="c" :value="c">{{ c }}</option>
            </select>
          </label>

          <div class="actions">
            <button class="btn primary" type="submit">{{ editId ? 'Actualizar' : 'Guardar' }}</button>
            <button v-if="editId" class="btn ghost" type="button" @click="() => { 
              form = { dni:'', nombres:'', apellidos:'', fechaNacimiento:'', genero:'F', ciudad:'Guayaquil' }; 
              editId = null; error = ''; ok = '' }">
              Cancelar
            </button>
          </div>
        </form>
      </section>

      <!-- TABLA -->
      <section class="card">
        <h2 class="card-title">Registros</h2>
        <div class="table-wrap">
          <table class="table">
            <thead>
              <tr>
                <th>DNI</th><th>Nombres</th><th>Apellidos</th><th>Fecha Nac.</th><th>Género</th><th>Ciudad</th><th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-if="personas.length === 0">
                <td class="empty" colspan="7">No hay registros todavía.</td>
              </tr>
              <tr v-for="p in personas" :key="p._id">
                <td>{{ p.dni }}</td>
                <td>{{ p.nombres }}</td>
                <td>{{ p.apellidos }}</td>
                <td>{{ p.fechaNacimiento?.slice(0,10) }}</td>
                <td>{{ p.genero }}</td>
                <td>{{ p.ciudad }}</td>
                <td class="row-actions">
                  <button class="btn small" @click="edit(p)">Editar</button>
                  <button class="btn small danger" @click="removeItem(p._id)">Eliminar</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </main>

    <footer class="footer">
      <span>© Derechos reservados — Sindy Parra 2025</span>
    </footer>
  </div>
</template>
