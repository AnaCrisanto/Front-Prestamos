<template>
  <div class="dashboard">
    <!-- Navbar en la parte superior -->
    <Navbar />

    <!-- Contenedor principal que incluye el Sidebar y el contenido -->
    <div class="main-container">
      <!-- Sidebar a la izquierda -->
      <Sidebar />

      <!-- Contenido principal -->
      <div class="content">
        <img src="@/assets/gato.gif" alt="Descripción de la imagen" class="image"/>
        <router-view />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useAuthStore } from "@/stores/auth";
import { useRouter } from "vue-router";
import Navbar from "@/components/Navbar.vue";
import Sidebar from "@/components/Sidebar.vue";

const authStore = useAuthStore();
const router = useRouter();

const logout = () => {
  authStore.logout();
  router.push("/login");
};
</script>

<style scoped>
.dashboard {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background: linear-gradient(to right, #6a11cb, #c625fc); /* Degradado azul-violeta */
}
.image{
  width: 300px;
  height: 150px;
}
.main-container {
  display: flex;
  flex: 1;
  overflow: hidden;
  gap: 12px;
  padding: 20px;
}

.content {
  flex: 1;
  padding: 24px;
  overflow-y: auto;
  background: #ffffff;
  border-radius: 16px;
  margin: 8px;
  box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease-in-out;
}

.content:hover {
  transform: translateY(-3px);
}

/* Sidebar con diseño más minimalista */
.sidebar {
  width: 240px;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  color: white;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
    font-size: 2em;
}

/* Navbar con diseño más ligero */
.navbar {
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(8px);
  color: white;
  padding: 16px 24px;
  font-size: 18px;
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-radius: 12px;
  margin: 8px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.15);
}

/* Botón de logout con un estilo más limpio */
.logout-button {
  background: #ff6b6b;
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: bold;
  transition: background 0.3s ease-in-out;
}

.logout-button:hover {
  background: #d9534f;
}
</style>

