<script>
import axios from 'axios'; // Asegúrate de importar axios

export default {
  data() {
    return {
      inputData: '',
      apiResponse: "",
    };
  },
  methods: {
    highlightResponse(highlightedText) {
      const sqlKeywordStyles = {
        'ERROR': 'text-red-500',
        'EXITO': 'text-green-500',
        'CREATE': 'text-blue-300',
        'DATABASE': 'text-green-300',
        'TABLE': 'text-slate-200',
        'PRIMARY KEY': 'text-purple-300',
        'AUTO_INCREMENT': 'text-pink-300',
        'NUMERIC': 'text-yellow-300',
        'TEXT': 'text-indigo-300',
        'DECIMAL': 'text-teal-300',
        'ALTER': 'text-orange-300',
        'DROP': 'text-rose-500',
        'INSERT INTO': 'text-lime-300',
        'VALUES': 'text-amber-300',
        'FOREIGN KEY': 'text-cyan-300',
        'REFERENCES': 'text-violet-300',

        // ... otras palabras clave ...
      };

      // Aplicar el estilo a cada palabra clave
      Object.entries(sqlKeywordStyles).forEach(([keyword, className]) => {
        const regex = new RegExp(`\\b${keyword}\\b`, 'g');
        highlightedText = highlightedText.replace(regex, `<span class="${className}">${keyword}</span>`);
      });

      return highlightedText;
    },
    async sendData() {
      console.log("Send " + this.inputData)
      try {
        const response = await axios.post(
          'http://localhost:8080/compile',
          this.inputData,
          {
            headers: {
              'Content-Type': 'text/plain', // Indicar que estás enviando texto plano
            },
          }
        );
        this.apiResponse = response.data;
        console.log('Exito:', response.data);
      } catch (error) {
        console.error('Hubo un error al enviar los datos:', error);
      }
    },
  },
};
</script>

<template>
  <div class="h-screen overflow-hidden">
    <div class=" h-[12vh] flex justify-between items-center mx-10 mb-5">
      <img class="w-16 h-16 my-auto" src="imagenes/antlr.svg" alt="">
      <p class="text-stone-100 text-4xl">Complilador <span class="text-[#00618A]">My</span><span
          class="text-[#E48E00]">SQL</span> en Español</p>
      <img class="w-24 h-auto my-auto" src="imagenes/mysql.png" alt="">
    </div>
    <div class="flex h-[90vh] w-full ">


      <div
        class="w-2/5 h-fit mx-10 mb-4 border border-gray-200 rounded-lg bg-gray-50 dark:bg-gray-700 dark:border-gray-600">
        <div class="px-4  py-2 bg-white rounded-t-lg dark:bg-gray-800">
          <textarea id="comment" rows="4" v-model="inputData"
            class="w-full h-[26rem] px-0 text-sm text-gray-900 bg-white border-0 dark:bg-gray-800 focus:ring-0 dark:text-white dark:placeholder-gray-500"
            placeholder="Ingresa tu consulta SQL" required></textarea>
        </div>
        <div class="flex items-center justify-between px-3 py-2 border-t dark:border-gray-600">
          <button @click="sendData"
            class="inline-flex items-center py-2.5 px-4 text-xs font-medium text-center text-white bg-blue-700 rounded-lg focus:ring-4 focus:ring-blue-200 dark:focus:ring-blue-900 hover:bg-blue-800">
            Post comment
          </button>
        </div>
      </div>

      <div class="w-3/5 mb-20 mx-10 overflow-auto border border-2 border-gray-500  rounded-lg p-5" v-if="true">
        <pre>
<h1 class="text-blue-500 font-semibold">Respuesta del API: </h1>
<span class="text-stone-300" v-html="highlightResponse(apiResponse)"></span>
      </pre>
      </div>
    </div>
  </div>
</template>

<style scoped>
::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  background-color: #F5F5F5;
  border-radius: 10px;
}

::-webkit-scrollbar {
  width: 10px;
  background-color: #f5f5f500;
  overflow-y: scroll;
}

::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-image: -webkit-gradient(linear,
      left bottom,
      left top,
      color-stop(0.44, rgb(122, 153, 217)),
      color-stop(0.72, rgb(73, 125, 189)),
      color-stop(0.86, rgb(28, 58, 148)));
}

textarea {
  border: none;
  overflow: auto;
  outline: none;

  -webkit-box-shadow: none;
  -moz-box-shadow: none;
  box-shadow: none;

  resize: none;
  /*remove the resize handle on the bottom right*/
}

.error-text {
  color: red;
  /* Color rojo para indicar error */
  font-weight: bold;
  /* Texto en negrita */
  font-style: italic;
  /* Texto en cursiva */
}

.success-text {
  color: green;
  /* Color verde para indicar éxito */
  font-weight: bold;
  /* Texto en negrita */
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}</style>
