<script setup>
import { ref, computed } from 'vue'

const lista = ref([
  { id: 1, nombre: "Juan", confirmado: true, genero: "F" },
  { id: 2, nombre: "Pedro", confirmado: false, genero: "M" },
  { id: 3, nombre: "Maria", confirmado: true, genero: "X" },
  { id: 4, nombre: "Jose", confirmado: true, genero: "F" },
  { id: 5, nombre: "Paula", confirmado: false, genero: "M" },
])

const tareasPendientes = ref([
  {id: 1, tarea: "comprar globos"},
  {id: 2, tarea: "encargar comida"},
  {id: 3, tarea: "contar vasos"}
])

const temas = ref([
  {id: 1, tema: "Dinosaurios", votos: 0},
  {id: 2, tema: "Metal", votos: 0},
  {id: 3, tema: "Los 80", votos: 0}
])

const nombre = ref("")
const genero = ref("")
const generos = ref(["M", "F", "X"])
const conf = ref("")
const filtroGen = ref("")
const gen = ref("")
let mostrarPendientes = ref(false)
let mensajePendientes = ref("Mostrar lista de tareas pendientes")

const agregar = () => {
  const nuevoInv = {
    id: lista.value[lista.value.length - 1].id + 1,
    nombre: nombre.value,
    confirmado: false,
    genero: genero.value
  }
  lista.value.push(nuevoInv)
}

const confirmarInvitado = (nombre) => {
  const invitadoAConfirmar = lista.value.find((inv) => inv.nombre === nombre)
  invitadoAConfirmar.confirmado = true
}
const cancelarInvitado = (nombre) => {
  const invitadoACancelar = lista.value.find((inv) => inv.nombre === nombre)
  invitadoACancelar.confirmado = false
}

const filtrarPorConf = computed(() => {
  let filtrado = []
  if (conf.value !== '') {
    filtrado = lista.value.filter(inv => inv.confirmado === conf.value)
  }
  return filtrado
})

const filtrarPorGenero = computed(() => {
  let filtrado = []
  if (filtroGen.value !== '') {
    filtrado = lista.value.filter(inv => inv.genero === filtroGen.value)
  }
  return filtrado
})

const totalConf = computed(() => {
  const confirmados =  lista.value.filter(inv => inv.confirmado === true)
  return confirmados.length
})

const mostrarPend = () => {
  if(mostrarPendientes.value === true){
    mostrarPendientes.value = false;
    mensajePendientes = "Mostrar lista de tareas pendientes"
  } else {
    mostrarPendientes.value = true;
    mensajePendientes = "Ocultar lista de tareas pendientes"
  }
}

const votarTema = (id) => {
  const temaElegido = temas.value.find((t) => t.id == id)
  temaElegido.votos++
}
</script>

<template>
  <div class="container">
    <div class="izquierda">
      <h1>Lista de invitados</h1>
      <div v-for="inv in lista">
        <li :class="{ confirmado: inv.confirmado, pendiente: !inv.confirmado }">
          nombre: {{ inv.nombre }} | confirmado: {{ inv.confirmado ? 'si' : 'no' }} | genero: {{ inv.genero }}
          <button v-if="!inv.confirmado" @click="confirmarInvitado(inv.nombre)">Confirmar?</button>
          <button v-if="inv.confirmado" @click="cancelarInvitado(inv.nombre)">Cancelar?</button>
        </li>
      </div>
      <h2>Filtrar</h2>
      <div class="filtrar">
        <div class="columna">
          <h3>Por estado de confirmacion</h3>
          <select v-model="conf">
            <option :value="''"></option>
            <option :value="true">Confirmado</option>
            <option :value="false">Pendiente</option>
          </select>
          <div v-if="filtrarPorConf.length > 0">
            <div v-for="inv in filtrarPorConf">
              <li :class="{ confirmado: inv.confirmado, pendiente: !inv.confirmado }">
                nombre: {{ inv.nombre }} | genero: {{ inv.genero }}
                <button v-if="!inv.confirmado" @click="confirmarInvitado(inv.nombre)">Confirmar?</button>
                <button v-if="inv.confirmado" @click="cancelarInvitado(inv.nombre)">Cancelar?</button>
              </li>
            </div>
          </div>
          <div v-else>No hay invitados con este filtro</div>
        </div>
        <div class="columna">
          <h3>Por genero</h3>
          <select v-model="filtroGen">
            <option :value="''"></option>
            <option v-for="gen in generos" :value="gen">{{ gen }}</option>
          </select>
          
            <div v-if="filtrarPorGenero.length > 0">
              <div v-for="inv in filtrarPorGenero">
                <li :class="{ confirmado: inv.confirmado, pendiente: !inv.confirmado }">
                  nombre: {{ inv.nombre }} | confirmado: {{ inv.confirmado ? 'si' : 'no' }}
                  <button v-if="!inv.confirmado" @click="confirmarInvitado(inv.nombre)">Confirmar?</button>
                  <button v-if="inv.confirmado" @click="cancelarInvitado(inv.nombre)">Cancelar?</button>
                </li>
              </div>
            </div>
            <div v-else>No hay invitados con este filtro</div>
          </div>
      </div>
    
      <div class="contador">
        <h2>Total invitados confirmados: {{ totalConf }}</h2>
      </div>
    
      <div>
        <h1>Agregar invitado</h1>
        <div>Nombre: <input type="text" name="nombre" v-model="nombre" /></div>
        <div>Genero:
          <select v-model="genero" name="genero">
            <option v-for="item in generos" :value="item">{{ item }}</option>
          </select>
        </div>
        <div>
          <button type="submit" @click="agregar()">Enviar</button>
        </div>
      </div>
    </div>
    <div class="derecha">

      <h1>Tareas pendientes</h1>
      <div class="mostrarLista">
        <button @click="mostrarPend()">{{ mensajePendientes }}</button>
        
        <div class="tareasPend" >
          <li v-show="mostrarPendientes" v-for="t in tareasPendientes">{{ t.tarea }}</li>
        </div>
    
      </div>
      <div class="temas">
        <h1>Votar temas</h1>
        <div class="listaTemas">
          <table>
            <tr>
            <th>
              Tema
            </th>
            <th>
              Cantidad de votos
            </th>
            <th>Votar</th>
          </tr>
          <tr v-for="tema in temas">
            <td>{{ tema.tema }}</td>
            <td>{{ tema.votos }}</td>
            <td><button v-on:click="votarTema(tema.id)">Votar</button></td>
          </tr>
          </table>
        </div>
      </div>
    </div>
  </div>

</template>

<style scoped>
#app{
  margin: 15px;
  width: 100%;
}

.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

li {
  list-style: none;
}

.confirmado {
  color: green;
}

.pendiente {
  color: red;
}

.filtrar {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.columna {
  gap: 1rem;
  align-items: stretch;
  justify-content: flex-start;
  max-width: 100%;
  padding: 1rem;
  border: 1px dashed #666;
}

.listaTemas {
  display: flex;
  justify-content: center;
}

table, th, td {
  border: 1px dashed #666;
  padding: 5px;
}
</style>
