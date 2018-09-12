<template>
  <div id="app" class="container">
    <div class="page-header">
      <h1> Lista de compras </h1>
    </div>
    <form v-if="is_signed">
      <button type="button" class="btn btn-danger" v-on:click="cerrarSesion()"> Cerrar sesión </button>
      <hr />
      <table class="table table-responsive">
        <thead>
          <tr>
            <th> Nombre </th>
            <th> Cantidad </th>
            <td>  </td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="compra in compras">
            <td>
              <input type="text" class="form-control" placeholder="Ingrese el nombre del producto" v-model="compra.nombre" /> 
            </td>
            <td>
              <input type="number" class="form-control" placeholder="Ingrese la cantidad" v-model="compra.cantidad" /> 
            </td>
            <td>
              <a href="javascript:void(0);" title="Modificar" v-if="validarCompra(compra)" v-on:click="modificar(compra)">
                <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
              </a>
              |
              <a href="javascript:void(0);" title="Eliminar" v-on:click="eliminar(compra)">
                <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
              </a>
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
              <a href="javascript:void(0);" title="Agregar" v-if="validarCompra(compra_nueva)" v-on:click="agregar()">
                <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
              </a>
            </td>
          </tr>
        </tbody>
      </table>
    </form>
    <form v-else class="form-signin" action="javascript:void(0)">
      <h2 class="form-signin-heading"> Iniciá sesión </h2>
      <label for="inputEmail" class="sr-only"> Email </label>
      <input type="email" id="inputEmail" class="form-control" placeholder="Email" v-model="usuario.email" />
      <label for="inputPassword" class="sr-only"> Contraseña </label>
      <input type="password" id="inputPassword" class="form-control" placeholder="Contraseña" v-model="usuario.contrasena">
      <button class="btn btn-lg btn-primary btn-block" type="submit" v-on:click="iniciarSesion()"> Ingresar </button>
    </form>
  </div>
</template>

<script>

import Firebase from 'firebase'

let config = {
  //TU CONFIGURACIÓN
}
let app = Firebase.initializeApp(config);
let db = app.database();

//let compras = db.ref('compras');

export default {
  name: 'App',
  /*
  firebase: {
    compras: compras
  },
  */
  data(){
    return {
      is_signed: false,      
      usuario: {
        email: '',
        contrasena: ''
      },
      compra_nueva: {
        nombre: '',
        cantidad: ''
      },
      compras: [],
    }
  },
  created: function(){
    var that = this;
    Firebase.auth().onAuthStateChanged(function(user) {
      if (user) {
        that.is_signed = true;
        that.$bindAsArray('compras', db.ref('compras/' + user.uid));
      } else {
        that.is_signed = false;
        that.compras = [];
      }
    });
  },
  methods:{
    iniciarSesion: function(){
      var that = this;
      Firebase.auth().signInWithEmailAndPassword(that.usuario.email, that.usuario.contrasena)
      .then(function(){
        toastr.success('Iniciaste sesión correctamente.', 'Aviso');
      }, function(){
        toastr.error('El correo o la contrasena son incorrectos.', 'Aviso');
      }).catch(function(error) {
        toastr.error('Error al intentar iniciar sesión.', 'Aviso');
      });
    },
    cerrarSesion: function(){
      Firebase.auth().signOut().then(function() {
        // Sign-out successful.
      }).catch(function(error) {
        toastr.error('Error al intentar cerrar sesión.', 'Aviso');
      });
    },
    agregar: function() {
      compras.push(this.compra_nueva, function(error){
        if (error){
          toastr.error('Error al intentar agregar el registro.', 'Aviso');
        }else{          
          toastr.success('Registro agregado correctamente.', 'Aviso');
        }
      });
      this.compra_nueva.nombre = '';
      this.compra_nueva.cantidad = ''; 
    },
    modificar: function(p_compra){      
      compras.child(p_compra['.key']).set({
        nombre: p_compra.nombre,
        cantidad: p_compra.cantidad
      }, function(error){
        if (error){
          toastr.error('Error al intentar modificar el registro.', 'Aviso');
        }else{          
          toastr.success('Registro modificado correctamente.', 'Aviso');
        }
      });      
    },
    eliminar: function(p_compra){
      compras.child(p_compra['.key']).remove(function(error){
        if (error){
          toastr.error('Error al intentar eliminar el registro.', 'Aviso');
        }else{          
          toastr.success('Registro eliminado correctamente.', 'Aviso');
        }
      });      
    },
    validarCompra: function(p_compra){
      return (
        p_compra.nombre.split(' ').join('') != '' &&
        !isNaN(parseInt(p_compra.cantidad, 10))
      );
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
#app a .glyphicon-plus, #app a .glyphicon-ok {
  color: #42b983;
  text-decoration: none;
}
#app a .glyphicon-remove {
  color: #ff0000;
}
.logo {
  width: 100px;
  height: 100px
}
</style>
