<template>
  <div class="materiales-view">
    <h1>Gestión de Materiales</h1>
    <button class="btn-crear" @click="showCreateModal = true">
      <span>+</span> Crear Material
    </button>

    <!-- Lista de materiales -->
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th></th>
            <th>Tipo</th>
            <th>Marca</th>
            <th>Modelo</th>
            <th>Estatus</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="material in materiales" :key="material.id">
            <td>{{ material.id }}</td>
            <td>{{ material.tipo_material }}</td>
            <td>{{ material.marca }}</td>
            <td>{{ material.modelo }}</td>
            <td>
              <span :class="`estatus ${material.estatus.toLowerCase()}`">
                {{ material.estatus }}
              </span>
            </td>
            <td>
              <button class="btn-editar" @click="editarMaterial(material)">
                Editar ✏️
              </button>
              <button class="btn-eliminar" @click="eliminarMaterial(material.id)">
                Eliminar ❌
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal para crear/editar material -->
    <div v-if="showCreateModal || materialEditado" class="modal">
      <div class="modal-content">
        <h2>{{ materialEditado ? 'Editar Material' : 'Crear Material' }}</h2>
        <form @submit.prevent="guardarMaterial">
          <div class="form-group">
            <label for="tipo_material">Tipo de Material:</label>
            <input v-model="formMaterial.tipo_material" id="tipo_material" required />
          </div>
          <div class="form-group">
            <label for="marca">Marca:</label>
            <input v-model="formMaterial.marca" id="marca" required />
          </div>
          <div class="form-group">
            <label for="modelo">Modelo:</label>
            <input v-model="formMaterial.modelo" id="modelo" required />
          </div>
          <div class="form-group">
            <label for="estatus">Estatus:</label>
            <select v-model="formMaterial.estatus" id="estatus" required>
              <option value="Disponible">Disponible</option>
              <option value="Prestado">Prestado</option>
              <option value="En Mantenimiento">En Mantenimiento</option>
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

interface Material {
  id: number;
  tipo_material: string;
  marca: string;
  modelo: string;
  estatus: string;
}

const materiales = ref<Material[]>([]);
const showCreateModal = ref(false);
const materialEditado = ref<Material | null>(null);
const formMaterial = ref({
  tipo_material: "",
  marca: "",
  modelo: "",
  estatus: "Disponible",
});
const mensaje = ref("");
const tipoMensaje = ref("");

// Obtener materiales
const obtenerMateriales = async () => {
  try {
    const response = await axios.get("http://localhost:8000/materials");
    materiales.value = response.data;
  } catch (error) {
    mostrarNotificacion("Error al obtener materiales", "error");
  }
};

// Crear o actualizar material
const guardarMaterial = async () => {
  try {
    if (materialEditado.value) {
      await axios.put(
        `http://localhost:8000/materials/${materialEditado.value.id}`,
        formMaterial.value
      );
      mostrarNotificacion("Material actualizado correctamente", "exito");
    } else {
      await axios.post("http://localhost:8000/materials", formMaterial.value);
      mostrarNotificacion("Material creado correctamente", "exito");
    }
    obtenerMateriales();
    cancelarModal();
  } catch (error) {
    mostrarNotificacion("Error al guardar material", "error");
  }
};

// Editar material
const editarMaterial = (material: Material) => {
  materialEditado.value = material;
  formMaterial.value = { ...material };
  showCreateModal.value = true;
};

// Eliminar material
const eliminarMaterial = async (id: number) => {
  try {
    await axios.delete(`http://localhost:8000/materials/${id}`);
    mostrarNotificacion("Material eliminado correctamente", "exito");
    obtenerMateriales();
  } catch (error) {
    mostrarNotificacion("Error al eliminar material", "error");
  }
};

// Cancelar modal
const cancelarModal = () => {
  showCreateModal.value = false;
  materialEditado.value = null;
  formMaterial.value = {
    tipo_material: "",
    marca: "",
    modelo: "",
    estatus: "Disponible",
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

onMounted(() => {
  obtenerMateriales();
});
</script>

<style scoped>
.materiales-view {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
  font-family: 'Roboto', sans-serif;
}

h1 {
  text-align: center;
  font-size: 2.5em;
  margin-bottom: 20px;
  color: #333;
}

.btn-crear {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px 25px;
  border-radius: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 1.2em;
  transition: background-color 0.3s;
}

.btn-crear:hover {
  background-color: #0056b3;
}

.table-container {
  overflow-x: auto;
  margin-top: 30px;
}

table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  background-color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

th,
td {
  padding: 15px;
  text-align: left;
  font-size: 1.1em;
}

th {
  background-color: #f8f9fa;
  color: #555;
  font-weight: 600;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
}

td {
  background-color: #fafafa;
  color: #666;
  border-bottom: 1px solid #ddd;
}

tr:nth-child(even) td {
  background-color: #f1f1f1;
}

.estatus {
  padding: 5px 15px;
  border-radius: 25px;
  text-transform: capitalize;
  font-weight: 600;
}

.estatus.disponible {
  background-color: #d4edda;
  color: #155724;
}

.estatus.prestado {
  background-color: #fff3cd;
  color: #856404;
}

.estatus.en-mantenimiento {
  background-color: #f8d7da;
  color: #721c24;
}

.btn-editar,
.btn-eliminar {
  border: none;
  background: none;
  cursor: pointer;
  font-size: 1.3em;
  margin: 0 10px;
  transition: color 0.3s;
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
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 40px;
  border-radius: 10px;
  width: 450px;
  font-size: 1.2em;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 25px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  font-size: 1.2em;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1.1em;
}

.modal-buttons {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
  margin-top: 20px;
}

.btn-guardar {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.1em;
  transition: background-color 0.3s;
}

.btn-guardar:hover {
  background-color: #218838;
}

.btn-cancelar {
  background-color: #dc3545;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.1em;
  transition: background-color 0.3s;
}

.btn-cancelar:hover {
  background-color: #c82333;
}

.notificacion {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 12px 20px;
  border-radius: 8px;
  color: white;
  font-weight: bold;
}

.notificacion.exito {
  background-color: #28a745;
}

.notificacion.error {
  background-color: #dc3545;
}
</style>
