

 <template>
  <div class="juego-container">
    <!-- TEMPORIZADOR -->
    <div class="temporizador-container">
      <div class="temporizador-card">
        
        <div class="controles-temporizador">
          <button 
            @click="iniciarTemporizador" 
            :disabled="temporizadorActivo || juegoTerminado"
            class="btn-control"
          >
            ▶️ Iniciar Juego
  
          </button>
        </div>
      </div>
    </div>


    <!-- MUÑECO DEL AHORCADO -->
    <div class="ahorcado-container">
      <div class="ahorcado">
        <!-- Base del patíbulo -->
        <div class="base"></div>
        
        <!-- Poste vertical -->
        <div class="poste" v-if="erroresCometidos >= 1"></div>
        
        <!-- Travesaño superior -->
        <div class="travesano" v-if="erroresCometidos >= 2"></div>
        
        <!-- Soga -->
        <div class="soga" v-if="erroresCometidos >= 3"></div>
        
        <!-- Cabeza -->
        <div class="cabeza" v-if="erroresCometidos >= 4"></div>
        
        <!-- Cuerpo -->
        <div class="cuerpo" v-if="erroresCometidos >= 5"></div>
        
        <!-- Brazo izquierdo -->
        <div class="brazo brazo-izquierdo" v-if="erroresCometidos >= 6"></div>
        
        <!-- Brazo derecho -->
        <div class="brazo brazo-derecho" v-if="erroresCometidos >= 7"></div>
        
        <!-- Pierna izquierda -->
        <div class="pierna pierna-izquierda" v-if="erroresCometidos >= 8"></div>
        
        <!-- Pierna derecha -->
        <div class="pierna pierna-derecha" v-if="erroresCometidos >= 9"></div>
        
        <!-- Cara (solo cuando está completo) -->
      
        <div class="cara" v-if="erroresCometidos >= 4">
          <div class="ojo ojo-izquierdo"></div>
          <div class="ojo ojo-derecho"></div>
          <div class="boca"></div>
        </div>
      </div>
      
      <!-- Etiqueta de errores -->
       <br><br><br>
      <!-- <div class="etiqueta-errores">
        Errores: {{ erroresCometidos }}/9
      </div> -->
    </div>

    <!-- PALABRA -->
    <div class="palabra-container">
      <p class="palabra">
        <span
          v-for="(letra, i) in letrasEncontradas"
          :key="i"
          class="letra-casilla"
          :class="{ 'letra-revelada': letra !== '_' && letra !== ' ' }"
        >
          {{ letra === ' ' ? '&nbsp;' : letra }}
        </span>
      </p>
      <p class="mensaje">haz perdido{{ mensaje }}</p>
    </div>

    <!-- TECLADO -->
    <div class="teclado">
      <button
        v-for="(letra, i) in teclado"
        :key="i"
        @click="presionarLetra(letra)"
        :disabled="letrasUsadas.includes(letra) || juegoTerminado || !temporizadorActivo"
        :class="{
          'letra-correcta': letrasCorrectas.includes(letra),
          'letra-incorrecta': letrasIncorrectas.includes(letra),
          'disabled': letrasUsadas.includes(letra) || juegoTerminado || !temporizadorActivo
        }"
      >
        {{ letra }}
      </button>
    </div>

    <!-- BOTONES -->
    <div class="botones-accion">
      <button @click="reiniciarJuegoCompleto" class="btn-reiniciar">🔄 Nuevo Juego</button>
      <button @click="$router.push('/inicio')" class="btn-inicio">🏠 Volver al Inicio</button>
    </div>

    <!-- INFO CATEGORÍA -->
    <div class="info-categoria" v-if="categoriaActual && nivelActual">
      <h3>📋 Información del Juego</h3>
      <p><strong>Categoría:</strong> {{ categoriaActual }}</p>
      <p><strong>Nivel:</strong> {{ nivelActual.toUpperCase() }}</p>
      <p><strong>Reglas:</strong> {{ reglasTexto }}</p>
      <p><strong>Tiempo actual:</strong> {{ tiempoFormateado }}</p>
      <!-- <p><strong>Palabra:</strong> {{ palabraAleatoria }}</p> -->
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed, watch, onUnmounted } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

// Variables del temporizador
const segundos = ref(0);
const temporizadorActivo = ref(false);
let intervalo = null;

// Variables del juego
const teclado = ref([
  "a","b","c","d","e","f","g","h","i","j","k","l","m",
  "n","ñ","o","p","q","r","s","t","u","v","w","x","y","z"
]);

const palabraAleatoria = ref("");
const letrasEncontradas = ref([]);
const letrasUsadas = ref([]);
const letrasCorrectas = ref([]);
const letrasIncorrectas = ref([]);
const intentosRestantes = ref(0);
const erroresCometidos = ref(0);
const maxErrores = ref(9);
const juegoTerminado = ref(false);
const mensaje = ref("");

// Variables para mostrar información
const nivelActual = ref("");
const categoriaActual = ref("");

// Computed para tiempo formateado
const tiempoFormateado = computed(() => {
  const minutos = Math.floor(segundos.value / 60)
  const segs = segundos.value % 60
  return `${minutos.toString().padStart(2, "0")}:${segs
    .toString()
    .padStart(2, "0")}`
})

function iniciarTemporizador() {
  if (temporizadorActivo.value || juegoTerminado.value) return;
  
  temporizadorActivo.value = true;
  intervalo = setInterval(() => {
    segundos.value++;
  }, 1000);
}

function pausarTemporizador() {
  if (!temporizadorActivo.value) return;
  
  temporizadorActivo.value = false;
  clearInterval(intervalo);
  intervalo = null;
}

function reiniciarTemporizador() {
  pausarTemporizador();
  segundos.value = 0;
  localStorage.removeItem("segundos");
}


// Observador para cuando el juego termina
// watch(juegoTerminado, (nuevoValor) => {
//   if (nuevoValor) {
//     pausarTemporizador();
//     console.log("Temporizador detenido porque el juego terminó");
//   }
// });
watch(segundos, (nuevoValor) => {
  localStorage.setItem("segundos", nuevoValor)
})


// Limpiar intervalo al desmontar
onUnmounted(() => {
  clearInterval(intervalo);
});

onMounted(() => {
  iniciarJuego();
});

// Función para verificar si se perdió
function verificarSiPerdio() {
  if (erroresCometidos.value >= maxErrores.value && !juegoTerminado.value) {
    juegoTerminado.value = true;
    mensaje.value = `💀 ¡Perdiste en ${tiempoFormateado.value}! La palabra era: "${palabraAleatoria.value}"`;
    // Revelar todas las letras
    letrasEncontradas.value = palabraAleatoria.value.split('');
    console.log("¡Jugador perdió!");
    return true;
  }
  return false;
}

// Función para verificar si se ganó
function verificarSiGano() {
  if (!letrasEncontradas.value.includes("_") && !juegoTerminado.value) {
    juegoTerminado.value = true;
    mensaje.value = `🎉 ¡Ganaste en ${tiempoFormateado.value}! ¡Felicidades!`;
    console.log("¡Jugador ganó!");
    return true;
  }
  return false;
}

function iniciarJuego() {
  const nivel = localStorage.getItem("nivelSeleccionado");
  const categoria = localStorage.getItem("categoriaSeleccionada");
  const palabrasCategoria = localStorage.getItem("palabrasCategoria");

  // Guardar información actual para mostrar
  nivelActual.value = nivel || "";
  categoriaActual.value = categoria || "";

  // Reiniciar estado del juego
  letrasUsadas.value = [];
  letrasCorrectas.value = [];
  letrasIncorrectas.value = [];
  juegoTerminado.value = false;
  mensaje.value = "";
  erroresCometidos.value = 0;
  maxErrores.value = 9;
  intentosRestantes.value = 9;

  // Reiniciar temporizador pero NO iniciarlo automáticamente
  reiniciarTemporizador();

  if (!palabrasCategoria) {
    console.warn("No hay palabras guardadas en localStorage");
    mensaje.value = "❌ No se encontraron palabras. Regresa a la selección.";
    setTimeout(() => {
      router.push('/inicio');
    }, 2000);
    return;
  }

  let palabras;
  try {
    palabras = JSON.parse(palabrasCategoria);
  } catch (e) {
    console.error("Error al convertir JSON de palabrasCategoria:", e);
    mensaje.value = "❌ Error al cargar las palabras. Intenta de nuevo.";
    return;
  }

  if (!Array.isArray(palabras) || palabras.length === 0) {
    console.warn("palabrasCategoria no contiene un array válido");
    mensaje.value = "❌ No hay palabras disponibles para esta categoría.";
    return;
  }

  // Elegir palabra aleatoria
  palabraAleatoria.value = palabras[Math.floor(Math.random() * palabras.length)];
  console.log("Categoría:", categoria);
  console.log("Nivel:", nivel);
  console.log("Palabra elegida:", palabraAleatoria.value);
  console.log("Longitud de la palabra:", palabraAleatoria.value.length);

  // Crear array de "_" con misma longitud, preservando espacios
  letrasEncontradas.value = palabraAleatoria.value.split("").map(caracter => {
    return caracter === ' ' ? ' ' : '_';
  });

  console.log("Intentos disponibles:", intentosRestantes.value);
  console.log("Letras encontradas iniciales:", letrasEncontradas.value);
}

// REGLAS DEL NIVEL
const reglasTexto = computed(() => {
  const nivel = localStorage.getItem("nivelSeleccionado") || nivelActual.value;
  
  switch(nivel) {
    case "facil": 
      return "9 intentos - Palabras cortas y comunes";
    case "medio": 
      return "9 intentos - Palabras de dificultad media";
    case "experto": 
      return "9 intentos - Palabras largas y complejas";
    default: 
      return "9 intentos - Nivel por defecto";
  }
});

function presionarLetra(letra) {
  if (juegoTerminado.value || letrasUsadas.value.includes(letra) || !temporizadorActivo.value) return;

  console.log(`Presionando letra: ${letra}`);
  
  // Añadir letra a usadas
  letrasUsadas.value.push(letra);
  
  // Normalizar para comparación (minúsculas, sin acentos)
  const letraNormalizada = letra.toLowerCase();
  const palabraNormalizada = palabraAleatoria.value.toLowerCase();
  
  // LETRA INCORRECTA
  if (!palabraNormalizada.includes(letraNormalizada)) {
    console.log(`Letra ${letra} incorrecta`);
    letrasIncorrectas.value.push(letra);
    intentosRestantes.value--;
    erroresCometidos.value++;

    mensaje.value = `❌ Letra "${letra}" incorrecta. Te quedan ${intentosRestantes.value} intentos`;
    
    // Verificar si perdió después del error
    if (verificarSiPerdio()) {
      return;
    }
    return;
  }

  // LETRA CORRECTA
  console.log(`Letra ${letra} correcta!`);
  letrasCorrectas.value.push(letra);
  mensaje.value = `✅ ¡Correcto! Letra "${letra}" encontrada`;

  // Actualizar letras encontradas
  const nuevaLetrasEncontradas = [...letrasEncontradas.value];
  for (let i = 0; i < palabraAleatoria.value.length; i++) {
    const caracterOriginal = palabraAleatoria.value[i];
    const caracterNormalizado = caracterOriginal.toLowerCase();
    
    if (caracterNormalizado === letraNormalizada) {
      nuevaLetrasEncontradas[i] = caracterOriginal; // Mantener mayúsculas/minúsculas originales
    }
  }
  letrasEncontradas.value = nuevaLetrasEncontradas;
  
  // Verificar si ganó después de actualizar las letras
  verificarSiGano();
  
  console.log("Letras correctas actuales:", letrasCorrectas.value);
  console.log("Letras incorrectas actuales:", letrasIncorrectas.value);
  console.log("Letras encontradas:", letrasEncontradas.value);
}

function reiniciarJuegoCompleto() {
  pausarTemporizador();
  reiniciarTemporizador();
  iniciarJuego();
  mensaje.value = "¡Juego reiniciado! Presiona 'Iniciar Juego' para comenzar.";
}
</script>

<style scoped>
.juego-container {
  background-image: url("./imag/fondo.png");
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  min-height: 100vh;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  color: white;
  font-family: 'Segoe UI', 'Arial', sans-serif;
}

/* TEMPORIZADOR */
.temporizador-container {
  width: 100%;
  max-width: 800px;
  margin-bottom: 20px;
}

.temporizador-card {
  background: rgba(0, 0, 0, 0.8);
  border-radius: 15px;
  padding: 20px;
  border: 2px solid rgba(255, 215, 102, 0.3);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(10px);
}

.tiempo-display {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  margin-bottom: 15px;
}

.icono-tiempo {
  font-size: 2.5rem;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

.tiempo {
  font-size: 3rem;
  font-weight: 800;
  color: #FFD166;
  font-family: 'Courier New', monospace;
  text-shadow: 0 2px 10px rgba(255, 215, 102, 0.5);
  background: rgba(0, 0, 0, 0.5);
  padding: 10px 20px;
  border-radius: 10px;
  border: 2px solid rgba(255, 215, 102, 0.2);
}

.controles-temporizador {
  display: flex;
  justify-content: center;
  gap: 15px;
  flex-wrap: wrap;
}

.btn-control {
  padding: 12px 20px;
  border: none;
  border-radius: 10px;
  background: linear-gradient(145deg, #0984e3, #6c5ce7);
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 1rem;
  min-width: 160px;
  justify-content: center;
}

.btn-control:hover:not(:disabled) {
  transform: translateY(-3px);
  box-shadow: 0 6px 15px rgba(9, 132, 227, 0.4);
}

.btn-control:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none !important;
}

/* INFORMACIÓN DEL JUEGO */
.info-juego {
  display: flex;
  justify-content: space-around;
  width: 100%;
  max-width: 800px;
  margin-bottom: 30px;
  padding: 15px;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 15px;
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.info-item {
  text-align: center;
  padding: 0 20px;
}

.contador {
  font-size: 2.8rem;
  font-weight: 800;
  color: #FFD166;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
  min-height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.texto {
  font-size: 0.9rem;
  color: #a0a0d0;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-top: 5px;
}

/* CONTENEDOR DEL AHORCADO */
.ahorcado-container {
  margin: 20px 0 40px 0;
  position: relative;
  width: 300px;
  height: 300px;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 15px;
  padding: 20px;
}

.ahorcado {
  width: 100%;
  height: 100%;
  position: relative;
}

/* ESTILOS PARA EL MUÑECO */
.base {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 180px;
  height: 15px;
  background: #8B4513;
  border-radius: 5px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.poste {
  position: absolute;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  width: 12px;
  height: 220px;
  background: #8B4513;
  border-radius: 5px;
}

.travesano {
  position: absolute;
  top: 40px;
  left: 50%;
  transform: translateX(-50%);
  width: 130px;
  height: 12px;
  background: #8B4513;
  border-radius: 5px;
}

.soga {
  position: absolute;
  top: 52px;
  left: 50%;
  transform: translateX(-50%);
  width: 3px;
  height: 40px;
  background: #654321;
}

.cabeza {
  position: absolute;
  top: 92px;
  left: 50%;
  transform: translateX(-50%);
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: #FFCC99;
  border: 3px solid #333;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.cuerpo {
  position: absolute;
  top: 142px;
  left: 50%;
  transform: translateX(-50%);
  width: 8px;
  height: 90px;
  background: #333;
}

.brazo {
  position: absolute;
  top: 150px;
  width: 50px;
  height: 8px;
  background: #333;
  border-radius: 5px;
}

.brazo-izquierdo {
  left: calc(50% - 60px);
  transform: rotate(45deg);
}

.brazo-derecho {
  left: calc(50% + 10px);
  transform: rotate(-45deg);
}

.pierna {
  position: absolute;
  top: 225px;
  width: 50px;
  height: 8px;
  background: #333;
  border-radius: 5px;
}

.pierna-izquierda {
  left: calc(50% - 60px);
  transform: rotate(45deg);
}

.pierna-derecha {
  left: calc(50% + 10px);
  transform: rotate(-45deg);
}

.cara {
  position: absolute;
  top: 92px;
  left: 50%;
  transform: translateX(-50%);
  width: 50px;
  height: 50px;
}

.ojo {
  position: absolute;
  width: 10px;
  height: 10px;
  background: #333;
  border-radius: 50%;
  top: 18px;
}

.ojo-izquierdo {
  left: 12px;
}

.ojo-derecho {
  right: 12px;
}

.boca {
  position: absolute;
  bottom: 12px;
  left: 50%;
  transform: translateX(-50%);
  width: 25px;
  height: 4px;
  background: #333;
  border-radius: 2px;
}

.etiqueta-errores {
  position: absolute;
  bottom: -35px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(255, 107, 107, 0.2);
  color: #FF6B6B;
  padding: 8px 20px;
  border-radius: 20px;
  font-weight: bold;
  font-size: 1rem;
  border: 1px solid rgba(255, 107, 107, 0.3);
}

/* PALABRA */
.palabra-container {
  text-align: center;
  margin: 20px 0 40px 0;
  width: 100%;
  max-width: 800px;
  padding: 0 10px;
}

.palabra {
  display: flex;
  gap: 8px;
  justify-content: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
  min-height: 80px;
}

.letra-casilla {
  width: 55px;
  height: 65px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.2rem;
  font-weight: 800;
  background: rgba(255, 255, 255, 0.1);
  border-bottom: 4px solid #FF6B6B;
  border-radius: 8px 8px 0 0;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  text-transform: uppercase;
  transition: all 0.3s ease;
  color: rgba(255, 255, 255, 0.3);
}

.letra-casilla.letra-revelada {
  background: rgba(76, 217, 100, 0.2);
  border-bottom-color: #4CD964;
  color: white;
  animation: revelarLetra 0.5s ease;
}

@keyframes revelarLetra {
  0% { transform: scale(0.8); opacity: 0; }
  70% { transform: scale(1.1); }
  100% { transform: scale(1); opacity: 1; }
}

.mensaje {
  font-size: 1.3rem;
  min-height: 40px;
  margin-top: 20px;
  padding: 12px 20px;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  text-align: center;
}

/* TECLADO */
.teclado {
  display: grid;
  grid-template-columns: repeat(9, 1fr);
  gap: 8px;
  max-width: 600px;
  margin: 20px auto 30px;
  padding: 20px;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 15px;
  backdrop-filter: blur(10px);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.teclado button {
  width: 48px;
  height: 48px;
  font-size: 1.3rem;
  font-weight: 700;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
  background: linear-gradient(145deg, #2D3436, #1E272E);
  color: white;
  text-transform: uppercase;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
}

.teclado button:hover:not(:disabled) {
  transform: translateY(-3px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
  background: linear-gradient(145deg, #3D4446, #2E373F);
}

.teclado button:active:not(:disabled) {
  transform: translateY(-1px);
}

.teclado button.letra-correcta {
  background: linear-gradient(145deg, #00b894, #00a085) !important;
  color: white;
}

.teclado button.letra-incorrecta {
  background: linear-gradient(145deg, #d63031, #c0392b) !important;
  color: white;
}

.teclado button.disabled {
  opacity: 0.4;
  cursor: not-allowed;
  transform: none !important;
}

/* BOTONES DE ACCIÓN */
.botones-accion {
  display: flex;
  gap: 20px;
  margin: 30px 0;
}

.botones-accion button {
  padding: 14px 28px;
  font-size: 1rem;
  font-weight: 600;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  min-width: 180px;
}

.btn-reiniciar {
  background: linear-gradient(145deg, #0984e3, #6c5ce7);
  color: white;
}

.btn-inicio {
  background: linear-gradient(145deg, #e17055, #d63031);
  color: white;
}

.botones-accion button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
}

/* INFO CATEGORÍA */
.info-categoria {
  background: rgba(255, 255, 255, 0.1);
  padding: 20px;
  border-radius: 12px;
  margin-top: 20px;
  width: 100%;
  max-width: 600px;
  backdrop-filter: blur(5px);
  border-left: 4px solid #4ECDC4;
}

.info-categoria h3 {
  margin: 0 0 15px 0;
  color: #FFD166;
  text-align: center;
}

.info-categoria p {
  margin: 8px 0;
  color: #a0a0d0;
  font-size: 0.95rem;
}

.info-categoria strong {
  color: #74b9ff;
}

/* RESPONSIVE */
@media (max-width: 768px) {
  .temporizador-card {
    padding: 15px;
  }
  
  .tiempo {
    font-size: 2.5rem;
  }
  
  .icono-tiempo {
    font-size: 2rem;
  }
  
  .btn-control {
    min-width: 140px;
    padding: 10px 15px;
  }
  
  .teclado {
    grid-template-columns: repeat(7, 1fr);
    gap: 6px;
    padding: 15px;
  }
  
  .teclado button {
    width: 42px;
    height: 42px;
    font-size: 1.1rem;
  }
  
  .letra-casilla {
    width: 45px;
    height: 55px;
    font-size: 1.8rem;
  }
  
  .info-juego {
    flex-direction: column;
    gap: 15px;
    padding: 15px;
  }
  
  .botones-accion {
    flex-direction: column;
    width: 100%;
    max-width: 250px;
  }
  
  .ahorcado-container {
    width: 250px;
    height: 250px;
  }
  
  .contador {
    font-size: 2.5rem;
  }
}

@media (max-width: 480px) {
  .controles-temporizador {
    flex-direction: column;
    align-items: center;
  }
  
  .btn-control {
    width: 100%;
    max-width: 200px;
  }
  
  .teclado {
    grid-template-columns: repeat(6, 1fr);
    gap: 5px;
    padding: 12px;
  }
  
  .teclado button {
    width: 38px;
    height: 38px;
    font-size: 1rem;
  }
  
  .letra-casilla {
    width: 35px;
    height: 45px;
    font-size: 1.6rem;
    gap: 5px;
  }
  
  .ahorcado-container {
    width: 220px;
    height: 220px;
  }
  
  .info-juego {
    padding: 10px;
  }
  
  .botones-accion button {
    padding: 12px 20px;
    min-width: 150px;
  }
  
  .mensaje {
    font-size: 1.1rem;
    padding: 10px 15px;
  }
  
  .tiempo {
    font-size: 2rem;
  }
}
</style>