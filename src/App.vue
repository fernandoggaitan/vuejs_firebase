<template>
  <div id="app" class="container">
    <div class="page-header">
      <h1> Lista de compras </h1>
    </div>
    <form>
      <table class="table table-striped">
        <thead>
          <tr>
            <th> Nombre </th>
            <th> Cantidad </th>
            <td>  </td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="c in compras">
            <td> {{ c.nombre }} </td>
            <td> {{ c.cantidad }} </td>
            <td>
              <button class="btn btn-danger" v-on:click="eliminar(c)"> Eliminar </button>
            </td>
          </tr>
          <tr>
            <td> 
              <input type="text" class="form-control" placeholder="Ingrese el nombre del producto" v-model="compra_nueva.nombre" />
            </td>
            <td>  
              <input type="number" class="form-control" placeholder="Ingrese la cantidad" v-model="compra_nueva.cantidad" />
            </td>
            <td>
              <button class="btn btn-success" v-bind:disabled="!compra_nueva_validada" v-on:click="agregar()"> Agregar </button>
            </td>
          </tr>
        </tbody>
      </table>
    </form>
  </div>
</template>

<script>

import Hello from './components/Hello'

import Firebase from 'firebase'

let config = {
  apiKey: "AIzaSyCRdssTYpYa_NaCupKxMSZ2MTSw1Cf9tuI",
  authDomain: "miko-quir.firebaseapp.com",
  databaseURL: "https://miko-quir.firebaseio.com",
  projectId: "miko-quir",
  storageBucket: "miko-quir.appspot.com",
  messagingSenderId: "248651513243"
}

let app = Firebase.initializeApp(config);
let db = app.database();
let compras = db.ref('compras');

export default {
  name: 'App',
  firebase: {
    compras: compras
  },
  data(){
    return {
      compra_nueva: {
        nombre: '',
        cantidad: ''
      }
    }
  },
  computed:{
    compra_nueva_validada: function(){
      return (
        this.compra_nueva.nombre.split(' ').join('') != '' &&
        !isNaN(parseInt(this.compra_nueva.cantidad, 10))
      );
    }
  },
  methods:{
    agregar: function() {
      compras.push(this.compra_nueva);
      this.compra_nueva.nombre = '';
      this.compra_nueva.cantidad = '';
    },
    eliminar: function(p_compra){
      compras.child(p_compra['.key']).remove();
    }
  }
}

</script>

<style>
html {
  height: 100%;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

#app {
  color: #2c3e50;
  margin-top: -100px;
  max-width: 600px;
  font-family: Source Sans Pro, Helvetica, sans-serif;
}

#app a {
  color: #42b983;
  text-decoration: none;
}

.logo {
  width: 100px;
  height: 100px
}
</style>
