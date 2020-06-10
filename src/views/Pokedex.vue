<template>
    <div class="container">
        <h1 class="text-danger">POKEDEX</h1>

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
                    
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data() {
        return {
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
        }
    }
}
</script>