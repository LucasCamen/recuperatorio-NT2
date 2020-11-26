<template>

  <section class="src-components-formulario">

    <div class="jumbotron">
      <h2>Ingreso de Pagos</h2>
      <hr />
      <br>
        
          <div class="form-group">
                <label for="nombre">Nombre del Cliente</label>
                <input 
                    type="text"
                    id="nombre"
                    class="form-control"
                    v-model="v.f.nombre.$model"
                >
                <div v-if="v.f.nombre.$error && v.f.nombre.$dirty" class="alert alert-danger mt-1">
                    <div v-if="v.f.nombre.required.$invalid">Este campo es requerido</div>
                    <div v-if="v.f.nombre.minLength.$invalid">Este campo debe tener al menos {{v.f.nombre.minLength.$params.min}} caracteres</div>
                    <div v-if="v.f.nombre.maxLength.$invalid">Este campo debe tener máximo {{v.f.nombre.maxLength.$params.max}} caracteres</div>
                </div>
          </div>
          
          <div class="form-group">
              <label for="documento">Numero de Documento:</label>
              <input 
                  type="text"
                  id="documento"
                  class="form-control"
                  v-model="v.f.documento.$model"
              >
              
              <div v-if="v.f.documento.$error && v.f.documento.$dirty" class="alert alert-danger mt-1">
                  <div v-if="v.f.documento.required.$invalid">Este campo es requerido</div>
                  <div v-if="v.f.documento.numeric.$invalid">Este campo debe ser numérico</div>
              </div>
          </div>

          <div class="form-group">
              <label for="importe">Monto a Pagar:</label>
              <input 
                  type="text"
                  id="importe"
                  class="form-control"
                  v-model="v.f.importe.$model"
              >
              <div v-if="v.f.importe.$error && v.f.importe.$dirty" class="alert alert-danger mt-1">
                  <div v-if="v.f.importe.required.$invalid">Este campo es requerido</div>
                  <div v-if="v.f.importe.numeric.$invalid">Este campo debe ser numérico</div>
              </div>
          </div>

          <div class="form-group">
              <label for="pagado">Pago realizado:</label>
              <input 
                  type="text"
                  id="pagado"
                  class="form-control"
                  v-model="v.f.pagado.$model"
              >
              <div v-if="v.f.pagado.$error && v.f.pagado.$dirty" class="alert alert-danger mt-1">
                  <div v-if="v.f.pagado.required.$invalid">Este campo es requerido</div>
                  <div v-if="v.f.pagado.numeric.$invalid">Este campo debe ser numérico</div>
              </div>
          </div>

    <h3 v-if="!pagos.length && !pidiendo" class="alert alert-success">
      No se encontraron pagos
    </h3>
    <div v-if="pagos.length" class="alert alert-info">
      <h2>Detalle de Pagos</h2>
      <hr />
      <br>

      <div class="table-responsive">
        <table class="table table-dark">
          <tr :style="{color:'cornflowerblue'}">
            <th>NOMBRE</th>
            <th>DNI</th>
            <th>MONTO A PAGAR</th>
            <th>PAGO REALIZADO</th>
            <th>SALDO DE LA DEUDA</th>

          </tr>
          <tr v-for="(pago,key) in pagos" :key="key">
            <td>{{pago.nombre}}</td>
            <td>{{pago.documento}}</td>
            <td>{{pago.importe}}</td>
            <td>{{ponerSignoPesos(pago.importe)}}</td>
            <td>{{fyhLocal(pago.createdAt)}}</td>
            <td>{{pago.pagado}}</td>
            <td>{{ponerSigno(pago.pagado)}}</td>
            <td>{{fyhLocal(pago.createdAt)}}</td>
            <td>{{this.diferencia}}</td>

          </tr>
        </table>
      </div>        
    </div>


</section>

</template>
<script>
  import { required, minLength, maxLength, numeric } from '@vuelidate/validators'
  import { useVuelidate } from '@vuelidate/core'
 

  export default  {
    name: 'src-components-formularioVue',
    props: [],
    created () {
        const rules = {
          f: {
            nombre: { 
              required,
              minLength: minLength(5),
              maxLength: maxLength(15)
            },
            documento: { 
              required,
              numeric
            },
            importe: { 
              required,
              numeric
            },
            pagado: { 
              required,
              numeric
            },
          }
        }
        const f = this.f
        this.v = useVuelidate(rules, { f } )
    },    
    data () {
      return {
          url : 'https://5fbed72f5923c90016e6b5fa.mockapi.io/Recuperatorio',
          f : {
            nombre : '',
            documento : '',
            importe : '',
            pagado : ''
          },
          v : null
      }
    },
  }
    import filters from '../filters.js'

  export default  {
    name: 'src-components-ver',
    props: [],
    mixins : [filters],
    mounted () {
        this.getDatosFormAxios()
    },
    data () {
      return {
          url : 'https://5fbed72f5923c90016e6b5fa.mockapi.io/Recuperatorio',
      }
    },
    methods: {
        sendDatosFormAxios(datos) {
            try {
              let res = this.axios.post(this.url, datos, {'content-type': 'application/json'})
              console.log(res.data)
            }
            catch(error) {
              console.log('HTTP POST ERROR', error)
            }
        },

        enviar() {
            this.v.$touch()
            if(!this.v.$invalid) {
              let form = this.f
              this.sendDatosFormAxios(form)
              this.resetForm()
              this.v.$reset()
            }
        },
        
        resetForm() {
            this.v.f.nombre.$model = ''
            this.v.f.documento.$model = ''
            this.v.f.importe.$model = ''
            this.v.f.pagado.$model = ''
        }
    },
  async getDatosFormAxios() {
            try {
              let res = await this.axios(this.url)
              console.log(res.data)
              this.pagos = res.data
            }
            catch(error) {
              console.log('HTTP GET ERROR', error)
            }
            finally {
              this.pidiendo = false
            }
        },
    computed: {
      diferencia() {
        this.pagos(pago => {
          importe - pagado
        });
        return {
          monto,
          color : (monto === 0)? 'green':
                  ( monto<0?'green': 'red')
        }
      },
    }



</script>

<style scoped lang="css">
  .src-components-formularioVue {

  }

  .jumbotron {
    background-color: rgb(114, 19, 51);
    color: white;
  }

  hr {
    background-color: #ddd;
  }

  pre {
    color: white;
  }

</style>
