<template>
  <div class="register-wrapper">
    <div class="register-card">
      <h1>Crear Cuenta</h1>
      <form @submit.prevent="handleRegister">

          <div class="form-group">
            <label for="nombre">Nombre *</label>
            <input type="text" v-model="nombre" id="nombre" required />
          </div>

          <div class="form-group">
              <label for="primerApellido">Primer Apellido *</label>
              <input type="text" v-model="primerApellido" id="primerApellido" required />
            </div>
          <div class="form-row">
            <div class="form-group">
              <label for="segundoApellido">Segundo Apellido</label>
              <input type="text" v-model="segundoApellido" id="segundoApellido" />
            </div>
          </div>

         <div class="form-group">
            <label for="nombreUsuario">Nombre de Usuario *</label>
            <input type="text" v-model="nombreUsuario" id="nombreUsuario" required />
          </div>

        <div class="form-group">
          <label for="correoElectronico">Correo Electrónico *</label>
          <input type="email" v-model="correoElectronico" id="correoElectronico" required />
        </div>

        <div class="form-group">
          <label for="contrasena">Contraseña *</label>
          <input type="password" v-model="contrasena" id="contrasena" required />
        </div>
         <div class="form-group">
            <label for="numeroTelefono">Número de Teléfono</label>
            <input type="text" v-model="numeroTelefono" id="numeroTelefono" />
          </div>
        <div class="form-row">

          <div class="form-group">
            <label for="tipoUsuario">Tipo de Usuario</label>
            <select v-model="tipoUsuario" id="tipoUsuario" required>
              <option value="Alumno">Alumno</option>
              <option value="Profesor">Profesor</option>
              <option value="Secretaria">Secretaria</option>
              <option value="Laboratorista">Laboratorista</option>
              <option value="Directivo">Directivo</option>
              <option value="Administrativo">Administrativo</option>
            </select>
          </div>
        </div>

        <button type="submit" class="btn-submit">Registrar</button>
      </form>

      <div class="login-link">
        ¿Ya tienes cuenta? <router-link to="/login">Inicia sesión</router-link>.
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const nombre = ref("");
const primerApellido = ref("");
const segundoApellido = ref("");
const nombreUsuario = ref("");
const correoElectronico = ref("");
const contrasena = ref("");
const numeroTelefono = ref("");
const tipoUsuario = ref("Alumno");
const router = useRouter();

const handleRegister = async () => {
  try {
    const userData = {
      nombre: nombre.value,
      primer_apellido: primerApellido.value,
      segundo_apellido: segundoApellido.value,
      tipo_usuario: tipoUsuario.value,
      nombre_usuario: nombreUsuario.value,
      correo_electronico: correoElectronico.value,
      contrasena: contrasena.value,
      numero_telefono: numeroTelefono.value,
    };

    await axios.post("https://crud-prestamos-v3us.onrender.com/users", userData);
    router.push("/login");
  } catch (error) {
    console.error("Error al registrar usuario", error);
  }
};
</script>

<style scoped>
.register-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #6a11cb 0%, #8d25fc 100%);
}

.register-card {
  background: #ffffff;
  padding: 2%;
  border-radius: 12px; /* Más pequeño */
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  width: 350px; /* Hacer el formulario aún más estrecho */
}

h1 {
  text-align: center;
  margin-bottom: 12px; /* Reducir margen */
  font-size: 1.4em; /* Reducir aún más el tamaño del título */
  color: #333;
}

.form-group {
  margin-bottom: 6px; /* Reducir aún más el espacio entre campos */
  font-size: 0.9em; /* Reducir tamaño de fuente */
}

.form-group label {
  display: block;
  font-weight: bold;
  margin-bottom: 3px; /* Reducir margen */
  color: #444;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 6px; /* Reducir el padding de los campos de entrada */
  border: 1px solid #ddd;
  border-radius: 8px; /* Ajustar más los bordes */
  font-size: 0.85em; /* Reducir más el tamaño de la fuente dentro de los campos */
}

.form-row {
  display: flex;
  gap: 10px; /* Reducir el espacio entre los grupos de campos */
}

.form-row .form-group {
  flex: 1;
}

.btn-submit {
  width: 100%;
  padding: 8px; /* Reducir el padding del botón */
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 6px; /* Bordes más redondeados */
  cursor: pointer;
  font-size: 1em; /* Reducir el tamaño del texto del botón */
  transition: background-color 0.3s;
}

.btn-submit:hover {
  background-color: #218838;
}

.login-link {
  margin-top: 10px; /* Reducir margen superior */
  text-align: center;
  font-size: 0.9em; /* Reducir tamaño de fuente */
}

.login-link a {
  color: #007bff;
  text-decoration: none;
  font-weight: bold;
}

.login-link a:hover {
  text-decoration: underline;
}
</style>
