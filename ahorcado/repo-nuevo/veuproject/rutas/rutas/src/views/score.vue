<template>
  <div class="ahorcado-wrap">
    <!-- contenedor relativo para superponer las imágenes -->
    <div class="ahorcado" :style="{ width: width + 'px', height: height + 'px' }">
      <!-- r1 es la base (madera) - siempre visible detrás -->
      <img class="base" src="./r1.PNG" alt="base" />

      <!-- piezas superpuestas (por orden de aparición) -->
      <img v-show="errors >= 1" class="piece p2" src="./r2.PNG" alt="soga y madera" />
      <img v-show="errors >= 2" class="piece p3" src="./r3.PNG" alt="cabeza 1" />
      <img v-show="errors >= 3" class="piece p4" src="./r4.PNG" alt="cabeza 2" />
      <img v-show="errors >= 4" class="piece p5" src="./r5.PNG" alt="cuello" />
      <img v-show="errors >= 5" class="piece p6" src="./r6.PNG" alt="tronco" />
      <img v-show="errors >= 6" class="piece p7" src="./r7.PNG" alt="brazo izquierdo" />
      <img v-show="errors >= 7" class="piece p8" src="./r8.PNG" alt="brazo derecho" />
      <img v-show="errors >= 8" class="piece p9" src="./r9.PNG" alt="pierna izquierda" />
      <img v-show="errors >= 9" class="piece p10" src="./r10.PNG" alt="pierna derecha" />
    </div>

    <!-- controles de ejemplo para probar la funcionalidad -->
    <div class="controls">
      <h3>Simulador de Errores</h3>
      <button @click="addError">Cometer Error ({{ errors }}/9)</button>
      <button @click="reset">Reiniciar Juego</button>
      <p>Errores cometidos: {{ errors }}</p>
      <p v-if="errors >= 9" class="game-over">¡JUEGO TERMINADO!</p>
      
      <!-- Para desarrollo: controles individuales -->
      <div class="debug-controls">
        <p><small>Debug: Mostrar piezas específicas</small></p>
        <div class="button-group">
          <button v-for="i in 9" :key="i" @click="setErrors(i)" class="small-btn">
            {{ i }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

/*
  Mapeo de errores a imágenes:
  - 0 errores: solo r1 (base)
  - 1 error: r2 (soga + parte de madera)
  - 2 errores: r3 (parte cabeza 1)
  - 3 errores: r4 (parte cabeza 2)
  - 4 errores: r5 (cuello)
  - 5 errores: r6 (tronco)
  - 6 errores: r7 (brazo izquierdo)
  - 7 errores: r8 (brazo derecho)
  - 8 errores: r9 (pierna izquierda)
  - 9 errores: r10 (pierna derecha) → juego terminado
*/

const width = 340    // ancho del contenedor (ajusta según tus imágenes)
const height = 420   // alto del contenedor (ajusta según tus imágenes)

// Número de errores cometidos (0-9)
const errors = ref(0)

// Contador de piezas visibles (solo para referencia)
const visibleCount = computed(() => {
  return Math.min(errors.value, 9)
})

// Función para agregar un error (usar en tu juego cuando se presiona una letra incorrecta)
function addError() {
  if (errors.value < 9) {
    errors.value++
  }
}

// Función para establecer un número específico de errores (para pruebas)
function setErrors(num) {
  errors.value = Math.min(num, 9)
}

// Función para reiniciar el juego
function reset() {
  errors.value = 0
}

// Ejemplo de cómo integrar con tu juego de ahorcado:
// 1. Cuando el jugador presiona una letra incorrecta, llama a addError()
// 2. Cuando el juego termina o se reinicia, llama a reset()
</script>

<style>
.ahorcado-wrap {
  display: flex;
  gap: 18px;
  align-items: flex-start;
  font-family: sans-serif;
  padding: 20px;
}

/* contenedor que mantiene posición relativa para superponer */
.ahorcado {
  position: relative;
  background: transparent;
  border-radius: 8px;
  overflow: hidden;
  /* opcional para ver tamaño en desarrollo */
  /* border: 1px dashed rgba(0,0,0,0.08); */
}

/* la base (r1) ocupa todo el contenedor */
.ahorcado img.base {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: contain; /* o cover según tus assets */
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
}

/* piezas superpuestas */
.ahorcado img.piece {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: contain;
  pointer-events: none; /* para que no interfieran */
  transition: opacity 0.3s ease; /* efecto suave al aparecer */
}

/* Si quieres controlar capas manualmente puedes usar z-index distintos */
.ahorcado img.p2 { z-index: 2; }  /* r2: soga/madera */
.ahorcado img.p3 { z-index: 3; }  /* r3: cabeza parte 1 */
.ahorcado img.p4 { z-index: 4; }  /* r4: cabeza parte 2 */
.ahorcado img.p5 { z-index: 5; }  /* r5: cuello */
.ahorcado img.p6 { z-index: 6; }  /* r6: tronco */
.ahorcado img.p7 { z-index: 7; }  /* r7: brazo izquierdo */
.ahorcado img.p8 { z-index: 8; }  /* r8: brazo derecho */
.ahorcado img.p9 { z-index: 9; }  /* r9: pierna izquierda */
.ahorcado img.p10{ z-index: 10; } /* r10: pierna derecha */

/* --- controles --- */
.controls {
  display: flex;
  flex-direction: column;
  gap: 12px;
  min-width: 200px;
  padding: 15px;
  background: #f7fafc;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.controls h3 {
  margin: 0 0 10px 0;
  color: #2d3748;
}

.controls button {
  padding: 10px 15px;
  border: none;
  background: #2b6cb0;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
  transition: background 0.2s;
}

.controls button:hover {
  background: #2c5282;
}

.controls button:nth-child(2) {
  background: #718096;
}

.controls button:nth-child(2):hover {
  background: #4a5568;
}

.controls p {
  margin: 5px 0;
  font-size: 14px;
  color: #4a5568;
}

.game-over {
  color: #e53e3e !important;
  font-weight: bold;
  font-size: 16px !important;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% { opacity: 1; }
  50% { opacity: 0.7; }
  100% { opacity: 1; }
}

/* Controles de debug */
.debug-controls {
  margin-top: 15px;
  padding-top: 15px;
  border-top: 1px solid #e2e8f0;
}

.debug-controls p {
  color: #718096;
  margin-bottom: 8px;
}

.button-group {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
}

.small-btn {
  padding: 5px 10px !important;
  font-size: 12px !important;
  background: #a0aec0 !important;
  min-width: 30px;
}

.small-btn:hover {
  background: #718096 !important;
}
</style>