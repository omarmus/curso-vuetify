<template>
  <section>
    <h2>Empresas</h2>
    <agregar :empresa="empresa" v-if="mostrar"></agregar>
    <div v-if="!mostrar">
      <v-btn @click="agregar" color="success">Agregar</v-btn>
      <v-data-table
        :headers="headers"
        :items="empresas"
      >
        <template slot="items" slot-scope="props">
          <td>{{ props.item.nombre }}</td>
          <td>{{ props.item.direccion }}</td>
          <td>
            <v-btn 
              type="button" 
              small 
              color="error" 
              @click="eliminar(props.item.id)">
              <v-icon>delete</v-icon>
            </v-btn>
            <v-btn
              type="button"
              small
              @click="editar(props.item.id)"
            >
              <v-icon>edit</v-icon>
            </v-btn>
          </td>
        </template>
      </v-data-table>
    </div>
  </section>
</template>

<script>
import axios from 'axios'
import Agregar from './Agregar'
import EventBus from '@/lib/event-bus'
const url = 'http://localhost:4000/api-rest/empresas';

export default {
  created () {
    this.lista()

    EventBus.$on('listar', (data) => {
      this.mostrar = false
      this.lista()
      console.log(data.hola)
    })
  },
  components: {
    Agregar
  },
  data () {
    return {
      empresas: [],
      headers: [
        { text: 'Nombre', value: 'nombre' },
        { text: 'Dirección', value: 'direccion' },
        { text: 'Acciones', sortable: false }
      ],
      mostrar: false,
      empresa: null
    }
  },
  methods: {
    lista () {
      axios.get(url)
      .then((respuesta) => {
        console.log(respuesta.data)
        this.empresas = respuesta.data
        console.log(this.empresas)
      })
    },
    agregar () {
      this.mostrar = true
      this.empresa = null
    },
    eliminar (id) {
      if (confirm('¿Eliminar?')) {
        axios.delete(`${url}/${id}`) // url + '/' + id
        .then(respuesta => {
          this.lista()
        })
      }
    },
    editar (id) {
      axios.get(`${url}/${id}`)
      .then(respuesta => {
        console.log('empresa', respuesta.data)
        const empresa = respuesta.data

        this.empresa = empresa;

        this.mostrar = true
      })
    }
  }
}
</script>