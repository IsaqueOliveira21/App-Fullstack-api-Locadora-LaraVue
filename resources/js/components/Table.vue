<template>
    <div>
        {{ $store.state.teste }}
        <table class="table table-hover">
            <thead>
                <tr>
                    <th scope="col" v-for="t, key in titulos" :key="key">{{ t.titulo}}</th>
                    <th scope="col" v-if="visualizar.visivel || remover.visivel || atualizar.visivel"> Ações </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="obj, chave in dadosFiltrados" :key="chave">
                    <td v-for="valor, chaveValor in obj" :key="chaveValor">
                        <span v-if="titulos[chaveValor].tipo == 'texto'">{{ valor }}</span>
                        <span v-if="titulos[chaveValor].tipo == 'data'">
                            {{ valor | formataDataTempoGlobal }}
                        </span>
                        <span v-if="titulos[chaveValor].tipo == 'imagem'">
                            <img :src="'/storage/'+valor" width="100px" height="100px">
                        </span>
                    </td>
                    <td v-if="visualizar.visivel || remover.visivel || atualizar.visivel">
                        <div class="btn-group">
                            <button v-if="visualizar.visivel" class="btn btn-outline-primary btn-sm" :data-toggle="visualizar.dataToggle" :data-target="visualizar.dataTarget" @click="setStore(obj)">Visualizar</button>
                            <button v-if="atualizar.visivel" class="btn btn-outline-success btn-sm" :data-toggle="atualizar.dataToggle" :data-target="atualizar.dataTarget" @click="setStore(obj)">Atualizar</button>
                            <button v-if="remover.visivel" class="btn btn-outline-danger btn-sm" :data-toggle="remover.dataToggle" :data-target="remover.dataTarget" @click="setStore(obj)">Remover</button>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        // Foi movido para o app.js
        /*
        filters: {
            formataDataTempo(data) {
                if(!data) {
                    return '';
                }
                data = data.split('T');
                let dataDia = data[0];
                let dataHora = data[1];

                dataDia = dataDia.split('-');
                dataDia = dataDia[2] + '/' + dataDia[1] + '/' + dataDia[0];

                dataHora = dataHora.split('.');
                dataHora = dataHora[0];

                return dataDia + ' ' + dataHora;
            }
        },
        */
        props: ['titulos', 'dados', 'atualizar', 'visualizar', 'remover'],
        methods: {
            setStore(obj) {
                this.$store.state.transacao.status = '';
                this.$store.state.transacao.mensagem = '';
                this.$store.state.transacao.dados = '';
                this.$store.state.item = obj;
            }
        },
        computed: {
            dadosFiltrados() {
                let campos = Object.keys(this.titulos);
                let dadosFiltrados = [];

                this.dados.map((item, chave) => {
                    let itemFiltrado = {}
                    campos.forEach(campo => {
                        itemFiltrado[campo] = item[campo] // Utilizar a sintaxe de array para atribuir valores a objetos
                    });
                dadosFiltrados.push(itemFiltrado);
                });
                return dadosFiltrados; // variavel, nao função
            }
        }
    }
</script>
