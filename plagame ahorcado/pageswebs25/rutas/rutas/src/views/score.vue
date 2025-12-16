 <template>
  <div class="frankenstein-theme">
    
    <div class="users-container">
  
      <div class="users-list">
        <div 
          v-for="(u, i) in usuarios" 
          :key="i" 
          class="user-item"
          :class="{ 'user-item-new': i === usuarios.length - 1 }"
        >
          <span class="user-number">#{{ i + 1 }}</span>
          <span class="user-bolt">⚡</span>
          <span class="user-text">{{ u }}</span>
          <span class="user-status">VIVO</span>
        </div>
        
        <div v-if="usuarios.length === 0" class="empty-list">
          <p>📝 No hay criaturas registradas aún...</p>
          <p class="empty-hint">¡Trae tu primer sujeto de experimento!</p>
        </div>
      </div>
      
      <div class="stats">
        <p>📊 Total de criaturas: {{ usuarios.length }}</p>
      </div>
    </div>

    <!-- Temporizador -->
    <div class="timer-container">
      <h2>⏱️ Temporizador </h2>
      <div class="timer">
        <span class="timer-value">{{ segundos }}</span>
        <span class="timer-unit">segundos</span>
      </div>
     
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const usuario = ref(null);
const usuarios = ref([]);

// Cargar usuarios desde localStorage
onMounted(() => {
  const data = localStorage.getItem('usuarios');
  usuarios.value = data ? JSON.parse(data) : [];
  usuario.value = localStorage.getItem('usuario');
});

const segundos = ref(0);

onMounted(() => {
  const guardado = localStorage.getItem("segundos");
  if (guardado !== null) {
    segundos.value = Number(guardado);
  }
});
</script>

<style scoped>
.frankenstein-theme {
  background: linear-gradient(135deg, #0a2f0a 0%, #1a5f1a 100%);
  min-height: 100vh;
  color: #e0f7e0;
  padding: 20px;
  font-family: 'Courier New', monospace;
  border: 8px solid #2d5a2d;
  border-image: repeating-linear-gradient(45deg, #4caf50, #2d5a2d 10px) 8;
}

.header {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px;
  background: rgba(0, 40, 0, 0.6);
  border-radius: 10px;
  border: 2px solid #4caf50;
}

.header h1 {
  color: #a5d6a7;
  margin: 0;
  font-size: 2.5em;
  text-shadow: 0 0 10px #4caf50;
}

.subtitle {
  color: #c8e6c9;
  font-style: italic;
  margin-top: 5px;
}

.current-user {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
}

.user-card {
  background: rgba(76, 175, 80, 0.2);
  border: 3px solid #4caf50;
  border-radius: 15px;
  padding: 20px;
  display: flex;
  align-items: center;
  gap: 20px;
  width: fit-content;
  backdrop-filter: blur(10px);
  box-shadow: 0 0 20px rgba(76, 175, 80, 0.3);
}

.user-icon {
  font-size: 3em;
}

.user-label {
  color: #a5d6a7;
  margin: 0;
  font-size: 0.9em;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.user-name {
  color: #ffffff;
  margin: 5px 0 0 0;
  font-size: 1.8em;
  font-weight: bold;
}

.users-container {
  background: rgba(0, 30, 0, 0.7);
  border-radius: 15px;
  padding: 25px;
  margin-bottom: 30px;
  border: 2px solid #388e3c;
}

.users-container h2 {
  color: #81c784;
  margin-top: 0;
  border-bottom: 2px dashed #4caf50;
  padding-bottom: 10px;
}

.users-list {
  margin: 20px 0;
}

.user-item {
  background: linear-gradient(90deg, rgba(76, 175, 80, 0.1), rgba(56, 142, 60, 0.1));
  border-left: 5px solid #4caf50;
  padding: 15px;
  margin: 10px 0;
  border-radius: 8px;
  display: flex;
  align-items: center;
  gap: 15px;
  transition: all 0.3s ease;
  animation: pulse 2s infinite;
}

.user-item:hover {
  background: linear-gradient(90deg, rgba(76, 175, 80, 0.3), rgba(56, 142, 60, 0.3));
  transform: translateX(5px);
}

.user-item-new {
  animation: highlight 2s ease-in-out;
  border-left-color: #ffeb3b;
}

@keyframes highlight {
  0% { background-color: rgba(255, 235, 59, 0.3); }
  100% { background-color: rgba(76, 175, 80, 0.1); }
}

@keyframes pulse {
  0%, 100% { box-shadow: 0 0 5px rgba(76, 175, 80, 0.5); }
  50% { box-shadow: 0 0 15px rgba(76, 175, 80, 0.8); }
}

.user-number {
  color: #ffeb3b;
  font-weight: bold;
  font-size: 1.2em;
}

.user-bolt {
  font-size: 1.5em;
  color: #ffeb3b;
}

.user-text {
  flex: 1;
  color: #ffffff;
  font-size: 1.1em;
  font-weight: bold;
}

.user-status {
  background: #1b5e20;
  color: #a5d6a7;
  padding: 5px 15px;
  border-radius: 20px;
  font-size: 0.8em;
  text-transform: uppercase;
  letter-spacing: 1px;
  border: 1px solid #4caf50;
}

.empty-list {
  text-align: center;
  padding: 40px;
  color: #81c784;
  font-style: italic;
}

.empty-hint {
  color: #a5d6a7;
  margin-top: 10px;
  font-size: 0.9em;
}

.stats {
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #2d5a2d;
  color: #a5d6a7;
  font-size: 1.1em;
}

.timer-container {
  background: rgba(0, 40, 0, 0.6);
  border-radius: 15px;
  padding: 25px;
  text-align: center;
  border: 2px solid #4caf50;
}

.timer-container h2 {
  color: #81c784;
  margin-top: 0;
}

.timer {
  font-size: 4em;
  color: #ffffff;
  font-weight: bold;
  text-shadow: 0 0 20px #4caf50;
  margin: 20px 0;
  font-family: 'Digital', monospace;
}

.timer-unit {
  font-size: 0.5em;
  color: #a5d6a7;
  margin-left: 10px;
}

.timer-hint {
  color: #c8e6c9;
  font-style: italic;
  margin-top: 10px;
  font-size: 0.9em;
}
</style>
 
 
 
 
 
 
 <!-- <template>

  <div>

  <p>Usuario actual: {{ usuario }}</p>

  <div>
    <p v-for="(u, i) in usuarios" :key="i">{{ u }}</p>
  </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const usuario = ref(null);
const usuarios = ref([]);

// Cargar usuarios desde localStorage
onMounted(() => {
  const data = localStorage.getItem('usuarios');
  usuarios.value = data ? JSON.parse(data) : [];
  usuario.value = localStorage.getItem('usuario');
});



const segundos = ref(0)

onMounted(() => {
  const guardado = localStorage.getItem("segundos")
  if (guardado !== null) {
    segundos.value = Number(guardado)
  }
})
</script>
 -->

