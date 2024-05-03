<script>
import axios from 'axios'; // Asegúrate de importar axios
export default {
  data() {
    return {

      tablaResponse: [{ "password": "$2y$10$Y4tNuSqEznOcuoMcSncA/eMFGCS.nBtvFrL.D1CDbBwuLzZW2b3UO", "updated_at": 1694586520000, "name": "Carlos Alberto Pardio", "created_at": 1694586520000, "email_verified_at": null, "id": 1, "remember_token": null, "email": "charlspardio11@gmail.com" }],
      apiResponse: null,

      tablaP: null

    };



  }, mounted() {
    this.getData();

  },

  methods: {

    async getData() {
      try {
        const response = await axios.get(
          'http://localhost:8080/tree'
        );
        this.apiResponse = response.data;
        console.log(this.apiResponse)
      } catch (error) {
        console.error('Hubo un error al enviar los datos:', error);
      }
    },
    async getTabla(database, tabla) {
      this.tabla = tabla;
      this.tablaP = tabla;

      this.data = database;
      try {
        const response = await axios.get('http://localhost:8080/tabla', {
          params: {
            database: database,
            tabla: tabla,
          },
        });
        console.log(response)
        console.error('datos:', response.data);
        if (response.data) {
          this.tablaResponse = response.data;
        } else {
          this.tablaResponse = null;
        }
      } catch (error) {
        console.error('Hubo un error al obtener los datos:', error);
        this.tablaResponse = null;
      }
    }, startForm(campos) {


      this.codigo_html =
        `<div class="max-w-lg mx-auto my-10 bg-white p-8 rounded-xl shadow shadow-slate-300">
        <label id="nTabla" class="text-lg text-black"> `+ this.tabla + `</label>
      <form class="space-y-4">`
      this.addFields(campos);

    },
    addFields(campos) {
      console.log(campos.length);


      campos.forEach(campo => {
        this.codigo_html += ` <div>
          <label for="`+ campo + `" class="text-sm font-semibold text-gray-600 block">` + campo + `</label>
          <input v-model="`+ campo + `" type="text" id="` + campo + `" class="w-full p-2 border border-gray-300 rounded mt-1 text-black">
        </div>
        `

      });
      this.endfor(campos);

    }, endfor() {
      this.codigo_html += `<button type="submit" class="w-full p-2 text-white bg-blue-600 hover:bg-blue-700 rounded">Enviar</button>
                        </form> </div> `

      this.$router.push({ name: 'form', query: { codigoHtml: this.codigo_html, nombreTabla: this.tabla, nombreBase: this.data } });

    },

  }
}
</script>
<template>
  <!-- Contenedor principal -->
  <div class="contenedor flex h-fit">
    <!-- Contenedor de Bases de Datos -->
    <div class="containera border-2 border-gray-500 p-9 bg-gray-800 shadow-lg">
      <details open>
        <summary>Mis Bases de Datos</summary>
        <template v-for="(lista, clave) in apiResponse" :key="clave">
          <div class="folder cursor-pointer">
            <details>
              <summary>{{ clave }}</summary>
              <div class="folder cursor-pointer">
                <p v-for="(item, index) in lista" :key="index" @click="getTabla(clave, item)">{{ item }}</p>
              </div>
            </details>
          </div>
        </template>
      </details>
    </div>
    <!-- Contenedor de Tabla -->
    <div class="cTable w-[70vw] p-10 h-[100vh] border-t-2 border-gray-500" v-if="tablaResponse != null">
      <table class="table-auto border-collapse w-full">
        <thead>
          <tr>
            <th class="border border-gray-700 bg-gradient-to-r from-blue-600 to-blue-800 px-4 py-2 text-gray-200"
              v-for="(key, index) in Object.keys(tablaResponse[0])" :key="index">
              {{ key }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, rowIndex) in tablaResponse" :key="rowIndex">
            <td class="border border-gray-700 bg-gray-700 px-4 py-2 text-white"
              v-for="(key, colIndex) in Object.keys(item)" :key="colIndex">
              {{ item[key] }}
            </td>
          </tr>
        </tbody>
      </table>
      <button class="bg-blue-600 p-2 rounded-md mt-6 shadow-md"
        @click="startForm(Object.keys(tablaResponse[0]))">Insertar</button>
    </div>

  </div>
</template>

<style scoped>
body {
  background: #222;
  color: #aaa;
}

.container {}

details {
  background: #333;
  border: 1px solid #444;
  padding: 0.5em;
  border-radius: 0.25em;
}

details>summary {
  color: #adf;
}

details[open]>summary {
  background: #555;
  color: #fff;
}

.folder p {
  padding: 0.25em 0;
  margin-left: 1em;
  border-left: 1px dotted #555;
}

table {
  box-shadow: rgba(0, 0, 0, 0.75) 0px 4px 8px -2px;
}

th {
  background: linear-gradient(45deg, #4b6cb7, #182848);
}

td {
  background: #2a2f36;
  color: #fff;
}

.cTable {
  overflow-x: auto;
  /* Permite desplazamiento horizontal si es necesario */
  width: 200rem
    /* Asegura que no se salga de la pantalla */
}
</style>
