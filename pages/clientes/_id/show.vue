<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Ver cliente</div>
      <div v-if="cliente" class="card-body">
        <div class="my-2">
          <label for="nome">Nome</label>
          <input
            id="nome"
            v-model="cliente.nome"
            disabled="disabled"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="email">Email</label>
          <input
            id="email"
            v-model="cliente.email"
            disabled="disabled"
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
            disabled="disabled"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="estado">Estado</label>
          <input
            id="estado"
            v-model="cliente.estado"
            disabled="disabled"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="cidade">Cidade</label>
          <input
            id="cidade"
            v-model="cliente.cidade"
            disabled="disabled"
            type="text"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="data_de_nascimento">Data de Nascimento</label>
          <input
            id="data_de_nascimento"
            v-model="cliente.data_de_nascimento"
            disabled="disabled"
            type="date"
            class="form-control"
          />
        </div>
        <div class="my-2">
          <label for="planos">Planos</label>
          <select
            id="planos"
            v-model="cliente.planos"
            class="form-control"
            multiple
          >
            <option
              v-for="plano in cliente.planos"
              :key="plano.id"
              disabled="disabled"
              :value="plano.id"
            >
              {{ plano.nome }}
            </option>
          </select>
        </div>

        <div class="my-2">
          <NuxtLink class="btn btn-primary" to="/clientes">Voltar</NuxtLink>
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
    }
  },
  mounted() {
    this.fetchCliente()
  },
  methods: {
    async fetchCliente() {
      try {
        const response = await this.$axios.get(
          `/clientes/${this.$route.params.id}`
        )

        this.cliente = response.data.data
      } catch (err) {
        console.log(err.response.data.errors)
      }
    },
  },
}
</script>
