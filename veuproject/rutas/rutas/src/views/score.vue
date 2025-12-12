<template>
  <div class="frankenstein-container">
    <div class="estado susto">Susto</div>
    <div class="estado espantado">Espantado</div>
    <div class="estado muerte">Muerte</div>
  </div>
</template>

<script setup>
// No se requiere lógica aún
</script>

<style scoped>
.frankenstein-container {
  display: flex;
  gap: 20px;
  justify-content: center;
  margin-top: 40px;
}

/* ESTILO GENERAL TIPO FRANKENSTEIN */
.estado {
  width: 160px;
  padding: 18px 0;
  text-align: center;
  font-size: 20px;
  font-weight: 900;
  color: #0a0a0a;
  background: linear-gradient(#6afc6a, #3c802e);
  border: 4px solid #1e3d17;
  border-radius: 12px;
  letter-spacing: 2px;
  box-shadow: 0px 0px 15px #3cff00 inset,
              0px 0px 10px #1f5b15;
  text-transform: uppercase;
  font-family: Impact, "Franklin Gothic Heavy", Arial Black, sans-serif;
  transition: 0.25s all;
  cursor: default;
  position: relative;
}

/* Costuras estilo Frankenstein */
.estado::after {
  content: "";
  position: absolute;
  width: 90%;
  height: 4px;
  background: #000;
  top: 50%;
  left: 5%;
  box-shadow: 0 0 2px black;
}

/* Animación al pasar el mouse */
.estado:hover {
  transform: scale(1.05);
  box-shadow: 0px 0px 25px #3cff00 inset,
              0px 0px 18px #2cff00;
}

/* Variantes */
.susto {
  background: linear-gradient(#9bff9b, #4f9d4f);
}

.espantado {
  background: linear-gradient(#7aff7a, #3d8b3d);
}

</style>