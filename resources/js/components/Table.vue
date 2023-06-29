<template>
    <div>
        <table class="table table-hover">
            <thead>
                <tr>
                    <th scope="col" v-for="t, key in titulos" :key="key" class="text-uppercase  ">{{ t }}</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="obj in dados" :key="obj.id">
                    <!--    
                    <th scope="row">{{ m.id }}</th>
                    <td>{{ m.nome }}</td>
                    <td><img :src="'/storage/'+m.imagem" width="100px" height="100px"></td>
                    -->
                    <td v-if="titulos.includes(chave)" v-for="valor, chave in obj" :key="chave">
                        <span v-if="chave === 'imagem'">
                            <img :src="'/storage/'+valor" width="100px" height="100px">
                        </span>
                        <span v-else>
                            {{ valor }}
                        </span>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        props: ['titulos', 'dados']
    }
</script>
