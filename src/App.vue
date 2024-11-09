<template>
  <main class="app">
    <section class="saludo">
      <h2 class="titulo">
        Que tal
        <input type="text" placeholder="Indique su nombre" v-model="nombre" />
        <!-- Con v-model unimos/agrupamos/adjuntamos/ENLAZAMOS con la variable declarada.
        Con {{ nombre }} debugeamos-->
      </h2>
    </section>

    <section class="lista">
      <h3>Crear una lista de tareas</h3>

      <!-- Formulario que cuando se ha SUBMIT  -->
      <form @submit.prevent="anidarLista">
        <h4>Que hay en tu lista de tareas?</h4>
        <input type="text" placeholder="Hacer la cena" v-model="entradaDatos" />

        <!-- {{ entradaDatos }} -->
        <h4>Elige una categoría para la tarea</h4>

        <div class="opciones">
          <!-- Opción 1 -->
          <label for="personal">
            <input
              type="radio"
              name="categoria"
              value="personal"
              v-model="entradaCategoria"
            />
            <span></span>
            <div>Personal</div>
          </label>

          <!-- Opción 2 -->

          <label for="profesional">
            <input
              type="radio"
              name="categoria"
              value="profesional"
              v-model="entradaCategoria"
            />
            <span></span>
            <div>Profesional</div>
          </label>

          <!-- Debug {{ entradaCategoria }} -->
        </div>

        <input type="submit" value="Añadir tarea" />
      </form>
    </section>
    <!--Debug {{ listaTareas }} -->

    <!-- Listamos las tareas -->
    <section class="listaTareas">
      <h3>Tareas</h3>
      <div class="lista">
        <!-- v-for enlazado con el ARRAY -->
        <div
          v-for="tarea in listaTareasAsc"
          :class="`tareasInd ${tarea.completado && 'completado'}`"
        >
          <label>
            <input type="checkbox" v-model="tarea.completado" />
            <span :class="`opcion ${tarea.categoria} `"></span>
          </label>

          <div class="listaContenido">
            <input type="text" v-model="tarea.datos" />
          </div>

          <div class="acciones">
            <!-- Añado evento de click -->
            <button class="borrar" @click="eliminarTarea(tarea)">Borrar</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
/*
ref --> Referencia, para controlar el ESTADO
onMounted --> Usado para cuando cargamos/renderizamos la pagina y los componentes.
Y accede al almacenamiento local
computed --> Para tener datos en orden, por ejemplo ordenado por tiempo de creación
watch --> Observará cambios que se hagan en ref y actualizará el almacenamiento local
*/
import { ref, onMounted, computed, watch } from "vue";

const listaTareas = ref([]); // Array
const nombre = ref(""); // String

const entradaDatos = ref("");
const entradaCategoria = ref(null);

// Obtenemos las tareas de forma ordenada por tiempo de creación
const listaTareasAsc = computed(() =>
  listaTareas.value.sort((a, b) => {
    return b.creadoEn - a.creadoEn;
  })
);

// Mediante la función watch vamos obtener el nombre en el input
watch(nombre, (nuevoValor) => {
  // Si nombre cambia, obtenemos el nuevoValor y lo insertamos en nombre
  localStorage.setItem("nombre", nuevoValor);
});

// Y mediante onMounted vamos a persistir el nombre en el input anterior, obteniendolo del localStorage
// Tambien el ARRAY de tareas
onMounted(() => {
  nombre.value = localStorage.getItem("nombre") || "";
  listaTareas.value = JSON.parse(localStorage.getItem("listaTareas")) || [];
});

// Función para añadir tareas, que solo añade al siguiente ARRAY -> const listaTareas = ref([]); // Array
const anidarLista = () => {
  // Si la tarea y la categorai están vacias
  if (entradaDatos.value.trim() === "" || entradaCategoria.value === null) {
    return;
  }

  // console.log("aaaa"); debug par ver si entra

  listaTareas.value.push({
    datos: entradaDatos.value,
    categoria: entradaCategoria.value,
    completado: false,
    creacion: new Date().getTime(),
  });

  entradaDatos.value = "";
  entradaCategoria.value = null;
};

// Función para borrar la tarea

const eliminarTarea = (tarea) => {
  // Miramos en todo el array, si coincide pues no lo añadimos(borramo)
  listaTareas.value = listaTareas.value.filter((t) => t !== tarea);
};

// Ahora me aseguro de guardarlo en el localStorage para no perder la información cuando se refresque

watch(
  listaTareas,
  (nuevoValor) => {
    // Si nombre cambia, obtenemos el nuevoValor y lo insertamos en nombre
    localStorage.setItem("listaTareas", JSON.stringify(nuevoValor));
    // Con deep podemos mirar dentro del ARRAY listaTareas para observar cambios
  },
  { deep: true }
);
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>
