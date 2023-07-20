<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">

                <card-component titulo="Busca de Marcas">
                    <template v-slot:conteudo>
                        <div class="row">
                            <div class="col-6 mb-3">
                                <input-container-component id="inputId" titulo="ID" id-help="idHelp" texto-ajuda="Informe o ID da marca (opcional).">
                                    <input type="number" class="form-control" id="inputId" aria-describedby="idHelp" placeholder="ID" v-model="busca.id">
                                </input-container-component>
                            </div>
                            <div class="col-6 mb-3">
                                <input-container-component id="inputNome" titulo="Nome da Marca" id-help="nomeHelp" texto-ajuda="Informe o nome da marca (opcional).">
                                    <input type="text" class="form-control" id="inputNome" aria-describedby="nomeHelp" placeholder="Nome da Marca" v-model="busca.nome">
                                </input-container-component>
                            </div>
                        </div>
                    </template>
                    <template v-slot:rodape>
                        <button type="submit" class="btn btn-primary btn-sm float-right" @click="pesquisar()">Pesquisar</button>
                    </template>
                </card-component>

                <card-component titulo="Relação de Marcas">
                    <template v-slot:conteudo>
                        <table-component
                         :dados="marcas.data"
                         :visualizar="{ visivel: true, dataToggle: 'modal', dataTarget:'#modalMarcaVisualizar' }"
                         :atualizar="{ visivel: true, dataToggle: 'modal', dataTarget:'#modalMarcaAtualizar' }"
                         :remover="{ visivel: true, dataToggle: 'modal', dataTarget:'#modalMarcaRemover' }"
                         :titulos="{
                            id: {titulo: 'ID', tipo: 'texto'},
                            nome: {titulo: 'Nome', tipo: 'texto'},
                            imagem: {titulo: 'Imagem', tipo: 'imagem'},
                            created_at: {titulo: 'Data de Criação', tipo: 'data'}
                         }">
                        </table-component>
                    </template>

                    <template v-slot:rodape>
                        <div class="row">
                            <div class="col-10">
                                <pagination-component>
                                    <li v-for="l, key in marcas.links" :key="key" :class="l.active ? 'page-item active' : 'page-item'" @click="paginacao(l)">
                                        <a class="page-link" href="#" v-html="l.label"></a>
                                    </li>
                                </pagination-component>
                            </div>
                            <div class="col-2">
                                <button type="button" class="btn btn-success btn-sm float-right" data-toggle="modal" data-target="#modalMarca">Adicionar</button>
                            </div>
                        </div>
                    </template>
                </card-component>
            </div>
        </div>

        <!-- Inicio modal cadastro marcas -->
        <modal-component id="modalMarca" titulo="Adicionar Marca">
            <template v-slot:alertas>
                <alert-component tipo="success" :detalhes="transacaoDetalhes" titulo="Cadastro realizado com sucesso!" v-if="transacaoStatus == 'adicionado'"></alert-component>
                <alert-component tipo="danger" :detalhes="transacaoDetalhes" titulo="Erro ao cadastrar! " v-if="transacaoStatus == 'erro'"></alert-component>
            </template>
            <template v-slot:conteudo>
                <div class="form-group">
                    <input-container-component id="novoNome" titulo="Nome da Marca" id-help="novoNomeHelp" texto-ajuda="Informe o nome da marca">
                        <input type="text" class="form-control" id="novoNome" aria-describedby="novoNomeHelp" placeholder="Nome da Marca" v-model="nomeMarca">
                    </input-container-component>
                </div>
                <div class="form-group">
                    <input-container-component id="novoImagem" titulo="Imagem" id-help="novoImagemHelp" texto-ajuda="(Opcional) Insira uma imagem PNG">
                        <input type="file" class="form-control-file" id="novoImagem" aria-describedby="novoImagemHelp" @change="carregarImagem($event)">
                    </input-container-component>
                </div>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-primary" @click="salvar()">Salvar</button>
            </template>
        </modal-component>
        <!-- Final modal cadastro marcas -->

        <!-- Inicio modal visualizar marcas -->
        <modal-component id="modalMarcaVisualizar" titulo="Visualizar Marca">
            <template v-slot:alertas>
            </template>
            <template v-slot:conteudo>
                <input-container-component titulo="ID">
                    <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                </input-container-component>
                <input-container-component titulo="Nome da Marca">
                    <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                </input-container-component>
                <input-container-component titulo="Imagem">
                    <img :src="'storage/'+$store.state.item.imagem" alt="" v-if="$store.state.item.imagem">
                </input-container-component>
                <input-container-component titulo="Data de criação">
                    <input type="text" class="form-control" :value="$store.state.item.created_at" disabled>
                </input-container-component>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
            </template>
        </modal-component>
        <!-- Final modal visualizar marcas -->

        <!-- Inicio modal remover marcas -->
        <modal-component id="modalMarcaRemover" titulo="Remover Marca">
            <template v-slot:alertas>
                <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                <alert-component tipo="danger" titulo="Erro na transação" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'error'"></alert-component>
            </template>
            <template v-slot:conteudo v-if="$store.state.transacao.status != 'sucesso'">
                <input-container-component titulo="ID">
                    <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                </input-container-component>
                <input-container-component titulo="Nome da Marca">
                    <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                </input-container-component>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-danger" @click="remover()" v-if="$store.state.transacao.status != 'sucesso'">Remover</button>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
            </template>
        </modal-component>
        <!-- Final modal remover marcas -->

        <!-- Inicio modal atualizar marcas -->
        <modal-component id="modalMarcaAtualizar" titulo="Atualizar Marca">
            <template v-slot:alertas>
                <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                <alert-component tipo="danger" titulo="Erro na transação" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'error'"></alert-component>
            </template>
            <template v-slot:conteudo>
                <div class="form-group">
                    <input-container-component id="atualizarNome" titulo="Nome da Marca" id-help="atualizarNomeHelp" texto-ajuda="Informe o nome da marca">
                        <input type="text" class="form-control" id="atualizarNome" aria-describedby="atualizarNomeHelp" placeholder="Nome da Marca" v-model="$store.state.item.nome">
                    </input-container-component>
                </div>
                <div class="form-group">
                    <input-container-component id="atualizarImagem" titulo="Imagem" id-help="atualizarImagemHelp" texto-ajuda="(Opcional) Insira uma imagem PNG">
                        <input type="file" class="form-control-file" id="atualizarImagem" aria-describedby="atualizarImagemHelp" @change="carregarImagem($event)">
                    </input-container-component>
                </div>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-primary" @click="atualizar()">Atualizar</button>
            </template>
        </modal-component>
        <!-- Final modal atualizar marcas -->

    </div>
</template>

<script>
    export default {
        /* MOVIDO PARA O BOOTSTRAP.JS PARA O AXIOS INTERCEPTOR
        computed: {
            token() {
                let token = document.cookie.split(";").find(indice => {
                    return indice.includes('token=');
                });

                token = token.split("=")[1];
                token = 'Bearer ' + token;
                return token;
            }
        },
        */
        data() {
            return {
                urlBase: 'http://127.0.0.1:8000/api/v1/marca',
                urlPaginacao: '',
                urlFiltro: '',
                nomeMarca: '',
                arquivoImagem: [],
                transacaoStatus: '',
                transacaoDetalhes: {},
                marcas: { data: [] },
                busca: { id: '', nome: '' },
            }
        },
        methods: {
            pesquisar() {
                let filtro = '';
                for(let chave in this.busca) {
                    if(this.busca[chave]) {
                        if(filtro != '') {
                            filtro += ';';
                        }
                        filtro += chave + ':like:' + this.busca[chave];
                    }
                }
                if(filtro != '') {
                    this.urlPaginacao = 'page=1'
                    this.urlFiltro = '&filtro='+filtro+'%';
                } else {
                    this.urlFiltro = '';
                }
                this.carregarLista();
            },
            remover() {
                let confirmacao = confirm("Deseja realmente remover o registro?");
                if(!confirmacao) {
                    return false;
                }

                let url = this.urlBase + '/' + this.$store.state.item.id;
                let formData = new FormData();
                let config = {
                    headers: {
                        //'Accept': 'application/json',
                        //'Authorization': this.token
                    }
                };
                formData.append('_method', 'delete');

                axios.post(url, formData, config)
                    .then(response => {
                        this.$store.state.transacao.status = 'sucesso';
                        this.$store.state.transacao.mensagem = response.data.msg;
                        this.carregarLista();
                    })
                    .catch(errors => {
                        console.log(errors.response);
                        this.$store.state.transacao.status = 'error';
                        this.$store.state.transacao.mensagem = errors.response.data.erro;
                    })
            },
            carregarLista() {
                let config = {
                    headers: {
                        //'Accept': 'application/json',
                        //'Authorization': this.token,
                    }
                }
                let url = this.urlBase + '?' + this.urlPaginacao + this.urlFiltro;
                axios.get(url, config)
                .then(response => {
                    this.marcas = response.data;
                    //console.log(response.data.data);
                })
                .catch(errors => {
                    console.log(errors);
                })
            },
            paginacao(l) {
                if(l.url) {
                    //this.urlBase = l.url;
                    this.urlPaginacao = l.url.split('?')[1];
                    this.carregarLista();
                }
            },
            carregarImagem(e) {
                this.arquivoImagem = e.target.files;
            },
            salvar() {
                let formData = new FormData();
                formData.append('nome', this.nomeMarca);
                formData.append('imagem', this.arquivoImagem[0]);
                let config = {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                        //'Accept': 'application/json',
                        //'Authorization': this.token,
                    }
                }
                axios.post(this.urlBase, formData, config) // o axios precisa da URL, Conteudo que será levado para o backend e por ultimo, as configuraçoes da requisição (method, accept e authorization)
                    .then(response => {
                        this.transacaoStatus = 'adicionado';
                        this.transacaoDetalhes = {
                            mensagem: 'Marca ' + response.data.nome + ' adicionada aos registros.'
                        }
                        //console.log(response);
                    })
                    .catch(errors => {
                        this.transacaoStatus = 'erro';
                        this.transacaoDetalhes = {
                            mensagem: errors.response.data.message,
                            dados: errors.response.data.errors
                        };
                        //console.log(errors.response.data.message);
                    })
            },
            atualizar() {
                let formData = new FormData();
                formData.append('_method', 'patch');
                formData.append('nome', this.$store.state.item.nome);
                if(this.arquivoImagem[0]) {
                    formData.append('imagem', this.arquivoImagem[0]);
                }

                let url = this.urlBase + '/' + this.$store.state.item.id;
                let config = {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                        //'Accept': 'application/json', <--- Foram movidos para bootstrap.js no axios intercepetor
                        //'Authorization': this.token <---
                    }
                }
                axios.post(url, formData, config)
                    .then(response => {
                        atualizarImagem.value = '';
                        this.$store.state.transacao.status = 'sucesso';
                        this.$store.state.transacao.mensagem = 'Marca atualizada com sucesso!';
                        this.carregarLista();
                    })
                    .catch(errors => {
                        this.$store.state.transacao.status = 'error';
                        this.$store.state.transacao.mensagem = errors.response.data.message;
                        this.$store.state.transacao.dados = errors.response.data.errors;
                        console.log(errors.response);
                    });
            }
        },
        mounted() {
            this.carregarLista()
        }
    }
</script>
