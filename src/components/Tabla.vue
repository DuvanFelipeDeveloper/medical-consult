<template>
  <div class="container">
    <table>
      <thead>
        <tr>
          <th>Consulta</th>
          <th>Diagnóstico</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(consulta, index) in consultas" :key="index">
          <td>{{ consulta.symptoms }}</td>
          <td>{{ consulta.diagnostic }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "ConsultasAnteriores",
  props: {
    consultas: {
      type: Array,
      required: true,
    },
  },
};
</script>

<style>
.container {
  max-height: 550px; /* Altura máxima del contenedor */
  overflow-y: auto; /* Agrega una barra de scroll vertical */
}

table {
  border-collapse: collapse;
  width: 100%;
  margin-top: 20px;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
  font-weight: bold;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}
</style>
