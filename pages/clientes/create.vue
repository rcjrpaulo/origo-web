<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Criar cliente</div>
      <div class="card-body">
        <div class="my-2">
          <label for="nome">Nome</label>
          <input
            id="nome"
            v-model="form.nome"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="email">Email</label>
          <input
            id="email"
            v-model="form.email"
            type="email"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="telefone">Telefone</label>
          <input
            id="telefone"
            v-model="form.telefone"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="estado">Estado</label>
          <select
            id="estado"
            v-model="estadoSelecionado"
            name="estado"
            class="form-control"
            @change="fetchCidades"
          >
            <option
              v-for="estado in estados"
              :key="estado.id"
              :value="estado.id"
            >
              {{ estado.nome }}
            </option>
          </select>
        </div>
        <div class="my-2">
          <label for="cidade">Cidade</label>
          <select
            id="cidade"
            v-model="form.cidade"
            name="cidade"
            class="form-control"
          >
            <option
              v-for="cidade in cidades"
              :key="cidade.id"
              :value="cidade.nome"
            >
              {{ cidade.nome }}
            </option>
          </select>
        </div>
        <div class="my-2">
          <label for="data_de_nascimento">Data de Nascimento</label>
          <input
            id="data_de_nascimento"
            v-model="form.data_de_nascimento"
            type="date"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="planos">Planos</label>
          <select
            id="planos"
            v-model="form.planos"
            class="form-control"
            multiple
          >
            <option v-for="plano in planos" :key="plano.id" :value="plano.id">
              {{ plano.nome }}
            </option>
          </select>
        </div>

        <div class="my-2">
          <NuxtLink class="btn btn-primary" to="/clientes">Voltar</NuxtLink>
          <span class="btn btn-success" @click="criarCliente">Criar</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        nome: '',
        email: '',
        telefone: '',
        estado: '',
        cidade: '',
        data_de_nascimento: '',
        planos: [],
      },
      estadoSelecionado: null,
      planos: [],
      estados: [],
      cidades: [],
    }
  },
  mounted() {
    this.fetchPlanos()
    this.fetchEstados()
  },
  methods: {
    async criarCliente() {
      try {
        const estadoSelecionadoArray = this.estados.filter(
          (estado) => this.estadoSelecionado === estado.id
        )
        this.form.estado = estadoSelecionadoArray[0].nome

        await this.$axios.post(`/clientes`, this.form)

        this.$router.push('/clientes')
      } catch (err) {
        if (Array.isArray(Object.keys(err.response.data))) {
          for (const chave of Object.keys(err.response.data)) {
            alert(err.response.data[chave][0])
          }
          return
        }

        console.log(err)
      }
    },
    async fetchPlanos() {
      const response = await this.$axios.get('/planos')
      this.planos = response.data.data
    },
    async fetchEstados() {
      this.estados = await this.$axios.$get(
        'https://servicodados.ibge.gov.br/api/v1/localidades/estados'
      )
    },
    async fetchCidades() {
      this.cidades = await this.$axios.$get(
        `https://servicodados.ibge.gov.br/api/v1/localidades/estados/${this.estadoSelecionado}/municipios`
      )
    },
  },
}
</script>
