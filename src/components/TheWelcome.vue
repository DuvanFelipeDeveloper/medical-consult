<template>
  <div class="container">
    <img class="logo" :src="require('@/assets/img/mario-square.png')" alt="Logo de Mario">
    <h1>Consulta médica</h1>
    <div class="form-group">
      <label for="consulta">Consulta:</label>
      <input type="text" id="consulta" name="consulta">
    </div>
    <div class="form-group">
      <label for="diagnostico">Diagnóstico:</label>
      <textarea id="diagnostico" name="diagnostico"></textarea>
    </div>
    <img class="image" :src="require('@/assets/img/mario-long.png')" alt="Imagen de Mario">
  </div>
</template>



<script>
export default {
  name: 'MarioComponent'
}
</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  text-align: center;
}

.logo {
  width: 80px;
  height: 80px;
  margin-right: 20px;
}

h1 {
  font-size: 2rem;
  font-weight: bold;
  margin: 20px 0;
}

.form-group {
  margin: 20px 0;
  text-align: left;
}

label {
  display: block;
  margin-bottom: 10px;
  font-weight: bold;
}

input[type="text"],
textarea {
  width: 100%;
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  resize: none;
}

.image {
  width: 200px;
  height: 80px;
  margin-left: 20px;
}
</style>
