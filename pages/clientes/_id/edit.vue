<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Editar cliente</div>
      <form action="#" @submit.prevent="atualizarCliente">
        <div v-if="cliente" class="card-body">
          <div class="my-2">
            <label for="nome">Nome</label>
            <input
              id="nome"
              v-model="cliente.nome"
              required="required"
              type="text"
              class="form-control"
              @invalid="mostraMsgDeErro('Nome obrigatório !')"
            />
          </div>
          <div class="my-2">
            <label for="email">Email</label>
            <input
              id="email"
              v-model="cliente.email"
              required="required"
              type="email"
              class="form-control"
              @invalid="mostraMsgDeErro('Email obrigatório !')"
            />
          </div>
          <div class="my-2">
            <label for="telefone">Telefone</label>
            <input
              id="telefone"
              v-model="cliente.telefone"
              v-mask="'(##) #########'"
              required="required"
              type="text"
              class="form-control"
              @invalid="mostraMsgDeErro('Telefone obrigatório !')"
            />
          </div>
          <div class="my-2">
            <label for="estado">Estado</label>
            <select
              id="estado"
              v-model="estadoSelecionado"
              required="required"
              name="estado"
              class="form-control"
              @change="fetchCidades"
              @invalid="mostraMsgDeErro('Estado obrigatória !')"
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
              required="required"
              name="cidade"
              class="form-control"
              @invalid="mostraMsgDeErro('Cidade obrigatória !')"
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
              required="required"
              type="date"
              class="form-control"
              @invalid="mostraMsgDeErro('Data de Nascimento obrigatória !')"
            />
          </div>
          <div class="my-2">
            <label for="planos">Planos</label>
            <multiselect
              id="planos"
              v-model="cliente.planos"
              required="required"
              track-by="nome"
              label="nome"
              :multiple="true"
              :options="planos"
            ></multiselect>
          </div>

          <div class="my-2">
            <NuxtLink class="btn btn-primary" to="/clientes">Voltar</NuxtLink>
            <button type="submit" class="btn btn-success">Salvar</button>
          </div>
        </div>
      </form>
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

        const planos = [...this.cliente.planos]
        const planosMappedArray = planos.map((plano) => plano.id)

        const updateData = { ...this.cliente, planos: planosMappedArray }

        await this.$axios.put(`/clientes/${this.cliente.id}`, updateData)
        this.$swal.fire(
          'Sucesso !',
          'Cliente atualizado com sucesso !',
          'success'
        )

        this.$router.push('/clientes')
      } catch (err) {
        const statusCode = err.response.status || '500'
        const message = err.response.data.error || 'Houve um erro inesperado'
        this.$swal.fire(`Erro ${statusCode}`, message, 'error')
      }
    },
    async fetchCliente() {
      try {
        const response = await this.$axios.get(
          `/clientes/${this.$route.params.id}`
        )

        this.cliente = response.data.data

        this.fetchEstados()
      } catch (error) {
        this.$swal.fire('Erro !', error, 'error')
      }
    },
    async fetchPlanos() {
      try {
        const response = await this.$axios.get('/planos')
        this.planos = response.data.data
      } catch (err) {
        const statusCode = err.response.status || '500'
        const message = err.response.data.error || 'Houve um erro inesperado'
        this.$swal.fire(`Erro ${statusCode}`, message, 'error')
      }
    },
    async fetchEstados() {
      try {
        const response = await this.$axios.$get(
          'https://servicodados.ibge.gov.br/api/v1/localidades/estados'
        )
        this.estados = response

        const estadoSelecionado = response.filter(
          (estado) => estado.nome === this.cliente.estado
        )
        this.estadoSelecionado = estadoSelecionado[0].id
      } catch (error) {
        this.$swal.fire('Erro !', error, 'error')
      }

      this.fetchCidades()
    },
    async fetchCidades() {
      try {
        this.cidades = await this.$axios.$get(
          `https://servicodados.ibge.gov.br/api/v1/localidades/estados/${this.estadoSelecionado}/municipios`
        )
      } catch (error) {
        this.$swal.fire('Erro !', error, 'error')
      }
    },
    mostraMsgDeErro(msg) {
      this.$swal.fire(`Erro`, msg, 'error')
    },
  },
}
</script>
