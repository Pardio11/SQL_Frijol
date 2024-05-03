<template>
  <div>
    <!-- Contenedor donde se insertará el formulario -->
    <div v-html="codigoHtml" ref="formContainer"></div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  computed: {
    codigoHtml() {
      return this.$route.query.codigoHtml;
    },  nombreTabla() {
    return this.$route.query.nombreTabla || 'NombreTablaDesconocido';
  },
  nombreBase() {
    return this.$route.query.nombreBase || 'NombreBaseDesconocido';
  },
    
  },
  data() {
    return {
      campos: [] // Aquí se almacenarán los nombres de los campos del formulario
    };
  },
  mounted() {
    this.$nextTick(() => {
      const form = this.$refs.formContainer.querySelector('form');
      if (form) {
        form.addEventListener('submit', this.handleSubmit);
        this.extractFormFields(form);
      }
    });
  },
  methods: {
    extractFormFields(form) {
      // Extrae los campos del formulario del HTML generado
      
      const inputs = form.querySelectorAll('input');
      this.campos = Array.from(inputs).map(input => input.name || input.id); // Suponiendo que los nombres o IDs de los inputs son los nombres de los campos
    },

    handleSubmit(event) {
    event.preventDefault();
      
    // Obteniendo el valor de nombreTabla de la URL
    const nombreTabla = this.nombreTabla;
    const nombreBase= this.nombreBase;


    // Aquí sigues con tu lógica para manejar los valores del formulario
    const formValues = {};
    this.campos.forEach(campo => {
      formValues[campo] = document.querySelector(`#${campo}`).value;
    });

    // Construyendo la cadena de texto
    let texto = `usar ${nombreBase}\n`; // Asumiendo 'tiendaSuper' como nombre de base de datos
    texto += `insertar tabla ${nombreTabla}\n`;

    for (const [key, value] of Object.entries(formValues)) {
      texto += `${key} = ${value}.\n`;
    }

    texto += 'cerrar';
    console.log("AAAAA");
    // Imprime el texto en la consola
    console.log(texto);
    this.sendData(texto);
    this.$router.push({ name: 'about'})
  },
  async sendData(texto) {
      console.log("Send " + texto)
      try {
        const response = await axios.post(
          'http://localhost:8080/compile',
          texto,
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
  beforeDestroy() {
    const form = this.$refs.formContainer.querySelector('form');
    if (form) {
      // Elimina el detector de eventos para evitar pérdidas de memoria
      form.removeEventListener('submit', this.handleSubmit);
    }
  }
};
</script>
