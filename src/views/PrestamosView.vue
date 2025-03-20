<template>
  <div class="prestamos-view">
    <h1>Gestión de Préstamos</h1>
    <button class="btn-crear" @click="showCreateModal = true">
      <span>+</span> Crear Préstamo
    </button>

    <!-- Lista de préstamos -->
    <div class="table-container">
      <table class="styled-table">
        <thead>
          <tr>
            <th>ID</th>
            <th>Usuario</th>
            <th>Material</th>
            <th>Fecha Préstamo</th>
            <th>Fecha Devolución</th>
            <th>Estatus</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="prestamo in prestamos" :key="prestamo.id">
            <td>{{ prestamo.id }}</td>
            <td>{{ obtenerNombreUsuario(prestamo.id_usuario) }}</td>
            <td>{{ obtenerNombreMaterial(prestamo.id_material) }}</td>
            <td>{{ formatFecha(prestamo.fecha_prestamo) }}</td>
            <td>{{ prestamo.fecha_devolucion ? formatFecha(prestamo.fecha_devolucion) : "No devuelto" }}</td>
            <td>
              <span :class="`estatus ${prestamo.estatus_prestamo.toLowerCase()}`">
                {{ prestamo.estatus_prestamo }}
              </span>
            </td>
            <td>
              <button class="btn-editar" @click="editarPrestamo(prestamo)">
                Editar ✏️
              </button>
              <button class="btn-eliminar" @click="eliminarPrestamo(prestamo.id)">
                Eliminar ❌
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal para crear/editar préstamo -->
    <div v-if="showCreateModal || prestamoEditado" class="modal">
      <div class="modal-content">
        <h2>{{ prestamoEditado ? 'Editar Préstamo' : 'Crear Préstamo' }}</h2>
        <form @submit.prevent="guardarPrestamo">
          <div class="form-group">
            <label for="usuario">Usuario:</label>
            <select v-model="formPrestamo.id_usuario" id="usuario" required>
              <option v-for="usuario in usuarios" :key="usuario.id" :value="usuario.id">
                {{ usuario.nombre }} {{ usuario.primer_apellido }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label for="material">Material:</label>
            <select v-model="formPrestamo.id_material" id="material" required>
              <option v-for="material in materiales" :key="material.id" :value="material.id">
                {{ material.tipo_material }} - {{ material.marca }} {{ material.modelo }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label for="estatus">Estatus:</label>
            <select v-model="formPrestamo.estatus_prestamo" id="estatus" required>
              <option value="Activo">Activo</option>
              <option value="Devuelto">Devuelto</option>
              <option value="Vencido">Vencido</option>
            </select>
          </div>
          <div class="modal-buttons">
            <button type="submit" class="btn-guardar">Guardar</button>
            <button type="button" class="btn-cancelar" @click="cancelarModal">
              Cancelar
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Notificación -->
    <div v-if="mensaje" :class="`notificacion ${tipoMensaje}`">
      {{ mensaje }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import axios from "axios";

interface Prestamo {
  id: number;
  id_usuario: number;
  id_material: number;
  fecha_prestamo: string;
  fecha_devolucion: string | null;
  estatus_prestamo: string;
}

interface Usuario {
  id: number;
  nombre: string;
  primer_apellido: string;
}

interface Material {
  id: number;
  tipo_material: string;
  marca: string;
  modelo: string;
}

const prestamos = ref<Prestamo[]>([]);
const usuarios = ref<Usuario[]>([]);
const materiales = ref<Material[]>([]);
const showCreateModal = ref(false);
const prestamoEditado = ref<Prestamo | null>(null);
const formPrestamo = ref({
  id_usuario: 0,
  id_material: 0,
  estatus_prestamo: "Activo",
});
const mensaje = ref("");
const tipoMensaje = ref("");

// Obtener préstamos
const obtenerPrestamos = async () => {
  try {
    const response = await axios.get("https://crud-prestamos-v3us.onrender.com/loans");
    prestamos.value = response.data;
  } catch (error) {
    mostrarNotificacion("Error al obtener préstamos", "error");
  }
};

// Obtener usuarios
const obtenerUsuarios = async () => {
  try {
    const response = await axios.get("https://crud-prestamos-v3us.onrender.com/users");
    usuarios.value = response.data;
  } catch (error) {
    mostrarNotificacion("Error al obtener usuarios", "error");
  }
};

// Obtener materiales
const obtenerMateriales = async () => {
  try {
    const response = await axios.get("https://crud-prestamos-v3us.onrender.com/materials");
    materiales.value = response.data;
  } catch (error) {
    mostrarNotificacion("Error al obtener materiales", "error");
  }
};

// Crear o actualizar préstamo
const guardarPrestamo = async () => {
  try {
    if (prestamoEditado.value) {
      await axios.put(
        `https://crud-prestamos-v3us.onrender.com//loans/${prestamoEditado.value.id}`,
        formPrestamo.value
      );
      mostrarNotificacion("Préstamo actualizado correctamente", "exito");
    } else {
      await axios.post("https://crud-prestamos-v3us.onrender.com/loans", formPrestamo.value);
      mostrarNotificacion("Préstamo creado correctamente", "exito");
    }
    obtenerPrestamos();
    cancelarModal();
  } catch (error) {
    mostrarNotificacion("Error al guardar préstamo", "error");
  }
};

// Editar préstamo
const editarPrestamo = (prestamo: Prestamo) => {
  prestamoEditado.value = prestamo;
  formPrestamo.value = { ...prestamo };
  showCreateModal.value = true;
};

// Eliminar préstamo
const eliminarPrestamo = async (id: number) => {
  try {
    await axios.delete(`https://crud-prestamos-v3us.onrender.com/loans/${id}`);
    mostrarNotificacion("Préstamo eliminado correctamente", "exito");
    obtenerPrestamos();
  } catch (error) {
    mostrarNotificacion("Error al eliminar préstamo", "error");
  }
};

// Cancelar modal
const cancelarModal = () => {
  showCreateModal.value = false;
  prestamoEditado.value = null;
  formPrestamo.value = {
    id_usuario: 0,
    id_material: 0,
    estatus_prestamo: "Activo",
  };
};

// Mostrar notificación
const mostrarNotificacion = (texto: string, tipo: string) => {
  mensaje.value = texto;
  tipoMensaje.value = tipo;
  setTimeout(() => {
    mensaje.value = "";
  }, 3000);
};

// Formatear fecha
const formatFecha = (fecha: string) => {
  return new Date(fecha).toLocaleDateString();
};

// Obtener nombre de usuario
const obtenerNombreUsuario = (idUsuario: number) => {
  const usuario = usuarios.value.find((u) => u.id === idUsuario);
  return usuario ? `${usuario.nombre} ${usuario.primer_apellido}` : "Desconocido";
};

// Obtener nombre de material
const obtenerNombreMaterial = (idMaterial: number) => {
  const material = materiales.value.find((m) => m.id === idMaterial);
  return material ? `${material.tipo_material} - ${material.marca} ${material.modelo}` : "Desconocido";
};

onMounted(() => {
  obtenerPrestamos();
  obtenerUsuarios();
  obtenerMateriales();
});
</script>

<style scoped>
.prestamos-view {
  padding: 15px;
  max-width: 1000px;
  margin: 0 auto;
  font-family: 'Roboto', sans-serif;
  text-align: center;
}

.btn-crear {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 8px 18px;
  border-radius: 5px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 1em;
}

.table-container {
  overflow-x: auto;
  margin-top: 15px;
}

.styled-table {
  width: 100%;
  border-collapse: collapse;
  background-color: rgb(228, 226, 226);
  color: #555;
  font-family: 'Roboto', sans-serif;
  font-size: 0.9em;
}

.styled-table th,
.styled-table td {
  padding: 8px;
  text-align: left;
  border-bottom: 1px solid #ffffff;
}

.styled-table th {
  background-color: #f8f9fa;
}

.styled-table tr:nth-child(even) {
  background-color: #f2f2f2;
}

.estatus {
  padding: 4px 8px;
  border-radius: 5px;
  font-weight: bold;
  font-size: 0.9em;
}

.estatus.activo {
  background-color: #28a745;
  color: white;
}

.estatus.devuelto {
  background-color: #17a2b8;
  color: white;
}

.estatus.vencido {
  background-color: #dc3545;
  color: white;
}

.btn-editar,
.btn-eliminar {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1em;
  color: #555;
}

.btn-editar {
  color: #69ad52;
}

.btn-eliminar {
  color: #dc3545;
}

.btn-editar:hover {
  color: #0056b3;
}

.btn-eliminar:hover {
  color: #c82333;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: white;
  padding: 15px;
  border-radius: 5px;
  max-width: 450px;
  width: 100%;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.modal-buttons {
  margin-top: 15px;
  display: flex;
  justify-content: space-between;
}

.btn-guardar,
.btn-cancelar {
  padding: 8px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.9em;
}

.btn-guardar {
  background-color: #28a745;
  color: white;
}

.btn-cancelar {
  background-color: #dc3545;
  color: white;
}

.notificacion {
  padding: 8px;
  margin-top: 15px;
  border-radius: 5px;
  text-align: center;
  font-size: 1em;
}

.notificacion.exito {
  background-color: #28a745;
  color: white;
}

.notificacion.error {
  background-color: #dc3545;
  color: white;
}
</style>
