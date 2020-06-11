<template> 
    <div class="container">
        <h1 class="text-danger">POKEDEX</h1>



        <form @submit.prevent="agregarPokemon()" v-if="!editar">
            <h3 class="text-warning text-left">Agregar PoKéMoN</h3>
            <div class="col-4 ml-4">
                <div class="form-group"> 
                    <input type="text" class="form-control" placeholder="Número en Pokédex" v-model="caractPokemon.numdex">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Nombre" v-model="caractPokemon.nombre">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Tipo" v-model="caractPokemon.tipo">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Debilidad" v-model="caractPokemon.debilidad">
                </div>


                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Region" v-model="caractPokemon.region">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Legendario" v-model="caractPokemon.legendario">
                </div>
            </div>
            <button class="btn btn-primary btn-sm mx-2 float-left" type="submit">Agregar</button><br>
        </form>

        <form @submit.prevent="editarPokemon(pokemonEditar)" v-else>
            <h3 class="text-warning text-left">Editar PoKéMoN</h3>
            <div class="col-4 ml-4">
                <div class="form-group"> 
                    <input type="text" class="form-control" placeholder="Número en Pokédex" v-model="pokemonEditar.numdex">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Nombre" v-model="pokemonEditar.nombre">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Tipo" v-model="pokemonEditar.tipo">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Debilidad" v-model="pokemonEditar.debilidad">
                </div>


                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Region" v-model="pokemonEditar.region">
                </div>

                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Legendario" v-model="pokemonEditar.legendario">
                </div>
            </div>
            
            <button class="btn btn-warning btn-sm mx-2 float-left" type="submit">Actualizar</button>
            <button class="btn btn-info btn-sm mx-2 float-left" type="submit" @click="editar = false">Cancelar</button><br>
        </form>




        <br>
        <table class="table table-hover">
            <thead class="thead-dark">
                <tr>
                    <th scope="col">Pokedex No.</th>
                    <th scope="col">Nombre</th>
                    <th scope="col">Tipo</th>
                    <th scope="col">Debilidad</th>
                    <th scope="col">Region</th>
                    <th scope="col">Legendario</th>
                    <th scope="col">Opciones</th>
                </tr>
            </thead>
           
            <tbody>
                <tr v-for="(item, index) in Pokemons" :key="index">
                    <th scope="row">{{ item.numdex }}</th>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.tipo }}</td>
                    <td>{{ item.debilidad }}</td>
                    <td>{{ item.region }}</td>
                    <td>{{ item.legendario }}</td>
                     <td class="text-center">
                        <button @click="activarEdicion(item._id)" class="btn btn-light btn-sm">Editar</button>
                        <p></p>
                        <button @click="eliminarPokemon(item._id)" class="btn btn-danger btn-sm">Eliminar</button>
                     </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data() {
        return {
            caractPokemon: {numdex: '', nombre: '', tipo: '', debilidad: '', region: '', legendario: ''},
            editar: false,
            pokemonEditar: [],
            Pokemons:[]
        }
    },
    created() {
        this.listarPokemon();
    },
    methods: {
        listarPokemon() {
            this.axios.get('/pokemon')

            .then( res => {
                console.log(res.data);
                this.Pokemons = res.data;
            })

            .catch(
                err => {console.log(e.response);
                })
        },
        agregarPokemon() {
            this.axios.post('/pokemon', this.caractPokemon)
            .then(res => {
                this.Pokemons.push(res.data)
                this.caractPokemon.numdex = '';
                this.caractPokemon.nombre = '';
                this.caractPokemon.tipo = '';
                this.caractPokemon.debilidad = '';
                this.caractPokemon.region = '';
                this.caractPokemon.legendario = '';
                swal('Agregado', 'Nuevo elemento registrado','success');
            })
            .catch(e => {
                console.log(e.response);
                if(e.response.data.error.errors.nombre.properties.message){
                    swal('Invalido', 'Nombre requerido','warning');
                } else {
                    wal('Error', 'Error, algo pasó y no sé qué es','warning');
                }
                swal('Invalido', 'Elemento no agregado','warning');
            })
        },
        eliminarPokemon(id) { 
            this.axios.delete(`/pokemon/${id}`)
            .then(res => {
                let index = this.Pokemons.findIndex( item => item._id === res.data._id )
                swal({
                title: 'El elemento será eliminado',
                text: '¿Esta seguro de eliminarlo?',
                icon: 'error',
                buttons: true,
                dangerMode: true
            }).then((willDelete) => {
                if(willDelete){
                    this.Pokemons.splice(index, 1)
                    swal({
                        title: 'Eliminado',
                        text: 'El registro se ha eliminado',
                        icon: 'success'
                    });
                } else {

                }
                })
            })
            .catch( e => {
                console.log(e.response);
                })
        },
        activarEdicion(id) {
            this.editar = true;
            console.log(id);
            this.axios.get(`pokemon/${id}`)
            .then(res => {
                this.pokemonEditar = res.data;
            })
            .catch(e => {
                console.log(e.response);
            })
        },
        editarPokemon(item) {
            this.axios.put(`/pokemon/${item._id}`, item)
            .then(res => {
                let index = this.Pokemons.findIndex(p => p._id === res.data._id)
                this.Pokemons[index].numdex = res.data.numdex;
                this.Pokemons[index].nombre = res.data.nombre;
                this.Pokemons[index].tipo = res.data.tipo;
                this.Pokemons[index].debilidad = res.data.debilidad;
                this.Pokemons[index].region = res.data.region;
                this.Pokemons[index].legendario = res.data.legendario;
                this.pokemonEditar = {};
                swal('Actualizado', 'Nuevo elemento actualizado','success');
                this.editar = false;
            })
            .catch(e => {
                console.log(e.response);
            })
        }

    }
}
</script>