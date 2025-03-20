<template>
  <div class="login-container">
    <div class="card">
      <h1>Iniciar Sesión</h1>
      <form @submit.prevent="handleLogin">
        <div class="form-group">
          <label for="username">
            <span class="icon">👤</span> Usuario:
          </label>
          <input type="text" v-model="username" id="username" placeholder="Ingresa tu usuario" required />
        </div>
        <div class="form-group">
          <label for="password">
            <span class="icon">🔒</span> Contraseña:
          </label>
          <input type="password" v-model="password" id="password" placeholder="Ingresa tu contraseña" required />
        </div>
        <button type="submit" class="btn-ingresar">
          Iniciar Sesión
        </button>
      </form>

      <!-- Enlace para ir al registro -->
      <div class="auth-links">
        <p>¿No tienes una cuenta?
          <router-link to="/users" class="auth-link">
            ¡Regístrate aquí!
          </router-link>
        </p>
      </div>

      <!-- Notificación -->
      <div v-if="mensaje" :class="`notificacion ${tipoMensaje}`">
        {{ mensaje }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useAuthStore } from "@/stores/auth";
import { useRouter } from "vue-router";

const username = ref("");
const password = ref("");
const authStore = useAuthStore();
const router = useRouter();
const mensaje = ref("");
const tipoMensaje = ref("");

const handleLogin = async () => {
  try {
    await authStore.login({ username: username.value, password: password.value });
    mostrarNotificacion("Inicio de sesión exitoso", "exito");
    router.push("/dashboard");
  } catch (error) {
    mostrarNotificacion("Error al iniciar sesión", "error");
  }
};

const mostrarNotificacion = (texto: string, tipo: string) => {
  mensaje.value = texto;
  tipoMensaje.value = tipo;
  setTimeout(() => {
    mensaje.value = "";
  }, 3000);
};
</script>
<style scoped>
/* Fondo de la página */
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #6a11cb 0%, #8d25fc 100%);
}

/* Estilo de la tarjeta de login */
.card {
  background-color: #ffffff;
  padding: 20px 30px; /* Reducir padding */
  border-radius: 12px; /* Reducir bordes */
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 300px; /* Reducir el ancho */
  text-align: center;
  transform: translateY(-5%);
  transition: all 0.3s ease-in-out;
}

.card:hover {
  transform: translateY(0);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

/* Estilo del título */
h1 {
  font-size: 1.6em; /* Reducir tamaño del título */
  color: #333;
  margin-bottom: 15px; /* Reducir margen */
  font-weight: 700;
  letter-spacing: 1px;
}

/* Estilo de los formularios */
.form-group {
  margin-bottom: 15px; /* Reducir margen entre campos */
}

.form-group label {
  display: flex;
  align-items: center;
  gap: 8px; /* Reducir espacio entre icono y texto */
  margin-bottom: 6px; /* Reducir margen */
  font-size: 1em; /* Reducir tamaño de la fuente */
  font-weight: bold;
  color: #555;
}

.form-group input {
  width: 100%;
  padding: 10px; /* Reducir padding */
  border: 2px solid #ddd;
  border-radius: 8px; /* Bordes más pequeños */
  font-size: 1em; /* Reducir tamaño de la fuente */
  color: #333;
  outline: none;
  transition: border 0.3s ease, box-shadow 0.3s ease;
}

.form-group input:focus {
  border-color: #2575fc;
  box-shadow: 0 0 8px rgba(37, 117, 252, 0.3);
}

/* Estilo del botón de login */
.btn-ingresar {
  width: 100%;
  padding: 10px; /* Reducir padding */
  background-color: #2575fc;
  color: white;
  border: none;
  border-radius: 8px; /* Bordes más pequeños */
  font-size: 1.1em; /* Reducir tamaño de la fuente */
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.btn-ingresar:hover {
  background-color: #1d4ed8;
  transform: scale(1.05);
}

/* Estilo para el enlace de registro */
.auth-links {
  margin-top: 15px; /* Reducir margen superior */
}

.auth-link {
  color: #2575fc;
  font-weight: bold;
  text-decoration: none;
  transition: color 0.3s ease, text-decoration 0.3s ease;
}

.auth-link:hover {
  color: #1d4ed8;
  text-decoration: underline;
}

/* Estilo de las notificaciones */
.notificacion {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 10px 15px; /* Reducir padding de notificación */
  border-radius: 5px;
  color: white;
  font-weight: bold;
  opacity: 0;
  animation: fadeIn 0.5s forwards;
}

.notificacion.exito {
  background-color: #28a745;
}

.notificacion.error {
  background-color: #dc3545;
}

@keyframes fadeIn {
  to {
    opacity: 1;
  }
}
</style>
