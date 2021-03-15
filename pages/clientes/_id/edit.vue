<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Editar cliente</div>
      <div v-if="cliente" class="card-body">
        <div class="my-2">
          <label for="nome">Nome</label>
          <input
            id="nome"
            v-model="cliente.nome"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="email">Email</label>
          <input
            id="email"
            v-model="cliente.email"
            type="email"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="telefone">Telefone</label>
          <input
            id="telefone"
            v-model="cliente.telefone"
            v-mask="'(##) #########'"
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
            v-model="cliente.cidade"
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
            v-model="cliente.data_de_nascimento"
            type="date"
            class="form-control"
          />
        </div>
        <div v-if="cliente.planosSelect" class="my-2">
          <label for="planos">Planos</label>
          <select
            id="planos"
            v-model="cliente.planosSelect"
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
          <span class="btn btn-success" @click="atualizarCliente">Salvar</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cliente: null,
      estadoSelecionado: null,
      planos: [],
      estados: [],
      cidades: [],
    }
  },
  mounted() {
    this.fetchCliente()
    this.fetchPlanos()
  },
  methods: {
    async atualizarCliente() {
      try {
        const estadoSelecionadoArray = this.estados.filter(
          (estado) => this.estadoSelecionado === estado.id
        )
        this.cliente.estado = estadoSelecionadoArray[0].nome
        this.cliente.planos = this.cliente.planosSelect

        await this.$axios.put(`/clientes/${this.cliente.id}`, this.cliente)

        this.$router.push('/clientes')
      } catch (err) {
        console.log(err.response.data.errors)
      }
    },
    async fetchCliente() {
      try {
        const response = await this.$axios.get(
          `/clientes/${this.$route.params.id}`
        )

        this.cliente = response.data.data

        this.cliente.planosSelect = this.cliente.planos.map((plano) => plano.id)

        this.fetchEstados()
      } catch (err) {
        console.log(err)
      }
    },
    async fetchPlanos() {
      const response = await this.$axios.get('/planos')
      this.planos = response.data.data
    },
    async fetchEstados() {
      const response = await this.$axios.$get(
        'https://servicodados.ibge.gov.br/api/v1/localidades/estados'
      )
      this.estados = response

      const estadoSelecionado = response.filter(
        (estado) => estado.nome === this.cliente.estado
      )
      this.estadoSelecionado = estadoSelecionado[0].id

      this.fetchCidades()
    },
    async fetchCidades() {
      this.cidades = await this.$axios.$get(
        `https://servicodados.ibge.gov.br/api/v1/localidades/estados/${this.estadoSelecionado}/municipios`
      )
    },
  },
}
</script>
