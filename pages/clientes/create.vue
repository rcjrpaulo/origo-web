<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Criar cliente</div>
      <form action="#" @submit.prevent="criarCliente">
        <div class="card-body">
          <div class="my-2">
            <label for="nome">Nome</label>
            <input
              id="nome"
              v-model="form.nome"
              required="required"
              type="text"
              class="form-control"
            />
          </div>
          <div class="my-2">
            <label for="email">Email</label>
            <input
              id="email"
              v-model="form.email"
              required="required"
              type="email"
              class="form-control"
            />
          </div>
          <div class="my-2">
            <label for="telefone">Telefone</label>
            <input
              id="telefone"
              v-model="form.telefone"
              v-mask="'(##) #########'"
              required="required"
              type="text"
              class="form-control"
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
              required="required"
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
              required="required"
              type="date"
              class="form-control"
            />
          </div>
          <div class="my-2">
            <label for="planos">Planos</label>
            <multiselect
              id="planos"
              v-model="form.planos"
              required="required"
              track-by="nome"
              label="nome"
              :multiple="true"
              :options="planos"
            ></multiselect>
          </div>

          <div class="my-2">
            <NuxtLink class="btn btn-primary" to="/clientes">Voltar</NuxtLink>
            <button type="submit" class="btn btn-success">Criar</button>
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
        if (estadoSelecionadoArray[0]) {
          this.form.estado = estadoSelecionadoArray[0].nome
        }

        const planos = [...this.form.planos]
        const planosMappedArray = planos.map((plano) => plano.id)

        const createData = { ...this.form, planos: planosMappedArray }

        await this.$axios.post(`/clientes`, createData)

        this.$swal.fire('Sucesso !', 'Cliente criado com sucesso !', 'success')

        this.$router.push('/clientes')
      } catch (err) {
        const statusCode = err.response.status || '500'
        const message = err.response.data.error || 'Houve um erro inesperado'
        this.$swal.fire(`Erro ${statusCode}`, message, 'error')
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
        this.estados = await this.$axios.$get(
          'https://servicodados.ibge.gov.br/api/v1/localidades/estados'
        )
      } catch (error) {
        this.$swal.fire('Erro !', error, 'error')
      }
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
  },
}
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
