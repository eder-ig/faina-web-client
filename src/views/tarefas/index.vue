<template>
  <v-container>
    <modal
      v-model="modal"
      title="Adicionar Tarefa"
      width="1200"
      @salvar="salvarTeste()"
    >
      <v-form>
        <validation-observer
          ref="observer"
        >
          <v-container
            fluid
            grid-list-xs
          >
            <v-row dense>
              <v-col
                cols="12"
                xl="2"
                lg="3"
                md="4"
                sm="5"
                xs="12"
              >
                <validation-provider
                  v-slot="{ errors }"
                  name="Field Name"
                  rules="required"
                  vid="fieldName"
                >
                    <!-- :disabled="!form.id" -->
                  <v-text-field
                    v-model="form.fieldName"
                    :error-messages="errors"
                    :hide-details="!errors.length"
                    @keydown.enter="salvarRegistro()"
                    class="required"
                    dense
                    outlined
                    label="Field Name"
                  />
                </validation-provider>
              </v-col>
            </v-row>
          </v-container>
        </validation-observer>
      </v-form>
    </modal>
    <v-data-table
      :headers="headers"
      :items="registros"
      :search="search"
      :loading="loading"
      class="elevation-0"
      sort-by="dataFim"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>
            Lista de Tarefas
          </v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          />
          <v-spacer />
        </v-toolbar>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          hide-details
          label="Filtro"
          single-line
        />
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
          class="mr-2"
          small
          @click="listarRegistros()"
        >
          <!-- @click="exibir(item.id)" -->
          mdi-pencil
        </v-icon>
        <v-icon
          small
          @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn
          color="primary"
        >
          Reset
        </v-btn>
      </template>
      <template v-slot:item.status="{ item }">
        <v-chip
          :color="getColorsStatus(item.status)"
          dark
        >
          {{ item.status }}
        </v-chip>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import { mapActions, mapState } from 'vuex'
export default {
  name: 'TarefasPage',

  data: () => ({
    form: {
      id: null,
      fieldName: null
    },
    modal: true,
    search: null,
    items: [
      { tipo: 'Chamado', classificacao: 'Alto', status: 'Aberto', etapa: 'Para Aprovação', projeto: 'Faina', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Melhorias', classificacao: 'Baixo', status: 'Aberto', etapa: '', projeto: 'Faina', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '30' },
      { tipo: 'Tarefa Interna', classificacao: 'Médio', status: 'Atendimento', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '50' },
      { tipo: 'Tarefa Interna', classificacao: 'Baixo', status: 'Concluido', etapa: 'Para Aprovação', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Chamado', classificacao: 'Baixo', status: 'Aguardando Informações', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '90' },
      { tipo: 'Manutenção', classificacao: 'Baixo', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '100' },
      { tipo: 'Chamado', classificacao: 'Alto', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Tarefa Interna', classificacao: 'Alto', status: 'Aberto', etapa: 'Aguardando Verisonamento', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Chamado', classificacao: 'Alto', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Melhorias', classificacao: 'Alto', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Tarefa Interna', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Manutenção', classificacao: 'Médio', status: 'Aberto', etapa: 'Iniciado', projeto: 'Faina', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Chamado', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Tarefa Interna', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Melhorias', classificacao: 'Médio', status: 'Aberto', etapa: 'Teste', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Chamado', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Tarefa Interna', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Chamado', classificacao: 'Médio', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Manutenção', classificacao: 'Alto', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' },
      { tipo: 'Melhorias', classificacao: 'Baixo', status: 'Aberto', etapa: '', projeto: '', dataInicio: '10/11/2020', dataFim: '12/11/2020', progresso: '10' }
    ],
    headers: [
      {
        text: 'Actions',
        align: 'center',
        sortable: false,
        value: 'actions',
        width: 61
      },
      {
        text: 'Titulo',
        align: 'start',
        sortable: false,
        value: 'titulo',
        width: 100
      },
      {
        text: 'Tipo',
        align: 'start',
        sortable: false,
        value: 'tipoNome',
        width: 150
      },
      {
        text: 'Classificação',
        align: 'start',
        sortable: false,
        value: 'classificacaoNome',
        width: 200
      },
      {
        text: 'Status',
        align: 'start',
        sortable: false,
        value: 'statusNome',
        width: 100
      },
      {
        text: 'Conteúdo',
        align: 'start',
        sortable: false,
        value: 'conteudo',
        width: 100
      },
      {
        text: 'Inicio',
        align: 'start',
        sortable: false,
        value: 'inicioCronograma',
        width: 100
      },
      {
        text: 'Fim',
        align: 'start',
        sortable: false,
        value: 'fimCronograma',
        width: 100
      },
      {
        text: 'Inicio Etapa',
        align: 'start',
        sortable: false,
        value: 'inicioEtapas'
      },
      {
        text: 'Fim Etapa',
        align: 'start',
        sortable: false,
        value: 'fimEtapas'
      }
    ],
    loading: false
  }),

  computed: {
    ...mapState('tarefas', [
      'registros',
      'tipos'
    ])
  },

  created () {
    this.listarRegistros()
  },

  methods: {
    ...mapActions('tarefas', [
      'listar'
    ]),
    async listarRegistros () {
      this.loading = true

      await this.listar()

      this.loading = false
    },
    async salvarTeste () {
      if (await this.$refs.observer.validate()) {
        const formulario = {
          id: this.form.id || undefined,
          name: this.fieldName || undefined
        }

        let resposta
        if (formulario.id) resposta = await this.editar(this.formulario)
        else resposta = await this.salvar(this.formulario)

        if (resposta && !resposta.erro) {
          this.listarRegistros()
        }
      }
    },
    async exibir (id) {
      const res = await this.exibir(id)

      if (res && !res.erro) {
        this.modal = true
      }
    },
    async deletar (id) {
      const res = await this.deletar(id)

      if (res && !res.erro) {
        this.listarRegistros()
      }
    },
    getColorsStatus (status) {
      if (status === 'Aberto') return 'info'
      else if (status === 'Atendimento') return 'success'
      else if (status === 'Concluido') return 'accent'
      else if (status === 'Aguardando Informações') return 'secondary'
    }
  }
}
</script>
