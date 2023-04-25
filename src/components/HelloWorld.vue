<template>
  <div class="card my-4 col-lg-11">
    <div class="card-body">
      <div class="row">
        <div class="">
          <!-- Columna del formulario -->
          <img class="logo" :src="logo" alt="Logo de Mario" />

          <h1>Consulta médica</h1>
          <div class="form-group">
            <label for="consulta">Consulta:</label>
            <input
              type="text"
              id="consulta"
              name="consulta"
              v-model="sintomas"
            />
          </div>
          <div class="form-group">
            <label for="diagnostico">Diagnóstico:</label>
            <textarea
              id="diagnostico"
              name="diagnostico"
              v-model="diagnostico"
              disabled
            ></textarea>
            <div v-if="cargando">Cargando...</div>
            <div>{{ duration }}</div>
          </div>
          <div class="center">
            <button class="btn-consultar" @click="consulta">Consultar</button>
          </div>

          <div class="select-container">
            <br>

            <label for="cloud-provider">Selecciona servidor:</label>
            <div class="select-wrapper">
              <select v-model="selectedProvider" id="cloud-provider">
                <option disabled value="">Select a Cloud Provider</option>
                <option v-for="provider in providers" :value="provider.url">
                  {{ provider.name }}
                </option>
              </select>
              <div>
                <a
                  v-if="selectedProvider"
                  :href="selectedProvider"
                  target="_blank"
                  >{{
                    providers.find((p) => p.url === selectedProvider).name
                  }}</a
                >
                <span v-else>Please select a provider</span>
              </div>
            </div>
          </div>

          <img class="image" :src="image" alt="Imagen de Mario" />
        </div>
      </div>
    </div>
  </div>

  <!-- Columna de la tabla de consultas anteriores -->
  <div class="card col-lg-15 my-4 ml-7" v-if="consultasAnteriores != null">
    <div class="card-body">
      <h2>Consultas anteriores</h2>
      <ConsultasAnteriores :consultas="consultasAnteriores" />
    </div>
  </div>
</template>

<script>
import MarioSquare from "@/assets/img/Mario.png";
import MarioLong from "@/assets/img/Banner Mario.png";
import ConsultasAnteriores from "@/components/Tabla.vue";

export default {
  name: "MarioComponent",
  data() {
    return {
      selectedProvider:
        "http://medicalconsultbackendapi-env.eba-ix9vmg3t.us-east-1.elasticbeanstalk.com",
      providers: [
        {
          name: "Azure",
          url: "https://medicalconsultbackend.azurewebsites.net",
        },
        {
          name: "AWS",
          url: "http://medicalconsultbackendapi-env.eba-ix9vmg3t.us-east-1.elasticbeanstalk.com",
        },
        {
          name: "Google Cloud",
          url: "https://unique-caldron-384515.uc.r.appspot.com",
        },
      ],
      cargando: false,
      sintomas: "",
      diagnostico: "",
      logo: MarioSquare,
      image: MarioLong,
      consultasAnteriores: null,
      duration: "",
    };
  },
  components: {
    ConsultasAnteriores,
  },
  methods: {
    infoTabla() {
      fetch(this.selectedProvider + "/consult", {
        headers: {
          "ngrok-skip-browser-warning": "true", // Agregar el encabezado aquí
        },
      })
        .then((response) => response.json())
        .then((data) => {
          // Aquí puede hacer algo con la respuesta recibida

          this.consultasAnteriores = data;
        })
        .catch((error) => {
          // Aquí maneje cualquier error que haya ocurrido
          console.error(error);
        });
    },

    async consulta() {
      let startTime = performance.now();

      try {
        this.cargando = true;
        const response = await fetch(this.selectedProvider + "/api/chatgpt", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            "ngrok-skip-browser-warning": "true",
          },
          body: JSON.stringify({
            inputText: this.sintomas,
          }),
        });
        const data = await response.json();
        // Aquí puede hacer algo con la respuesta recibida

        this.diagnostico = data.responseText;
        this.guardarconsulta();
        this.cargando = false;
      } catch (error) {
        // Aquí maneje cualquier error que haya ocurrido
        this.diagnostico =
          "Hubo un problema al procesar tu solicitud. Por favor, inténtalo de nuevo más tarde.";
        this.cargando = false;
      }

      let endTime = performance.now();
      this.duration = endTime - startTime;
    },

    async guardarconsulta() {
      try {
        const response = await fetch(this.selectedProvider + "/consult/save", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            "ngrok-skip-browser-warning": "true",
          },
          body: JSON.stringify({
            symptoms: this.sintomas,
            diagnostic: this.diagnostico,
          }),
        });
        const data = await response.json();
        // Aquí puede hacer algo con la respuesta recibida
        console.log(data);
        this.consultasAnteriores = null;
        this.infoTabla();
      } catch (error) {
        // Aquí maneje cualquier error que haya ocurrido
        console.log(error);
      }
    },
  },
  created() {
    this.infoTabla();
  },
};
</script>

<style>
.card {
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  margin: 20px auto;
  max-width: 600px;
  background-color: rgba(255, 255, 255, 0.8);
}

.card-body {
  padding: 20px;
  text-align: center;
  color: black;
}

.separado {
  margin-left: 10px;
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

.btn-consultar {
  background-color: #0072c6;
  color: #fff;
  border: none;

  border-radius: 20px;
  padding: 10px 20px;
  font-size: 1.2rem;
  cursor: pointer;
  box-shadow: 0 4px 4px rgba(0, 0, 0, 0.25);
  transition: background-color 0.3s, transform 0.3s;
}

.btn-consultar:hover {
  background-color: #00599e;
  transform: translateY(-2px);
}

.btn-consultar:active {
  background-color: #00599e;
  transform: translateY(0);
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.25);
}

.image {
  width: 200px;
  height: 80px;
  margin-left: 20px;
}

.select-container {
  display: flex;
  flex-direction: column;
}

label {
  font-weight: bold;
}

.select-wrapper {
  position: relative;
}

select {
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 8px;
  font-size: 16px;
  cursor: pointer;
  appearance: none;
  background-color: transparent;
}

select:focus {
  outline: none;
  border-color: #007aff;
  box-shadow: 0 0 0 3px rgba(0, 122, 255, 0.1);
}

.selected-provider {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  padding: 8px;
  font-size: 16px;
  font-weight: bold;
  color: #007aff;
  pointer-events: none;
  display: flex;
  align-items: center;
}

.selected-provider span {
  color: #aaa;
}
</style>
