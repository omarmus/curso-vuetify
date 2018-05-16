<template>
  <v-form
    ref="form" 
    lazy-validation
    @submit="agregar">
    <v-text-field
      label="Nombre"
      v-model="nombre"
      required
      :rules="[v => !!v || 'Campo requerido']"
    ></v-text-field>
    <v-text-field
      label="Dirección"
      v-model="direccion"
      required
      :rules="[v => !!v || 'Campo requerido']"
    ></v-text-field>
    <v-btn type="button" @click="cancel">Cancelar</v-btn>
    <v-btn type="submit" color="primary"><v-icon>add</v-icon> Guardar</v-btn>
  </v-form>
</template>

<script>
import axios from 'axios'
import EventBus from '@/lib/event-bus'
const url = 'http://localhost:4000/api-rest/empresas';

export default {
  props: {
    empresa: {
      type: Object,
      default: null
    }
  },
  mounted () {
    if (this.empresa) {
      this.nombre = this.empresa.nombre
      this.direccion = this.empresa.direccion
      this.id = this.empresa.id
    }
  },
  data () {
    return {
      nombre: '',
      direccion: ''
    }
  },
  methods: {
    agregar () {
      if (this.$refs.form.validate()) {
        const data = {
          nombre: this.nombre,
          direccion: this.direccion
        }
        if (this.id) { // editar
          axios.put(`${url}/${this.id}`, data)
          .then(respuesta => {
            this.cancel()
          })
        } else { // guardar 
          axios.post(url, data)
          .then(respuesta => {
            console.log(respuesta)
            this.cancel()
          })
        }
        
      }
    },
    cancel () {
      this.$refs.form.reset()
      EventBus.$emit('listar', { hola: 'Editar' })
    }
  }
}
</script>

