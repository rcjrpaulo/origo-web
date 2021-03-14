<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Clientes</div>
      <div class="card-body">
        <NuxtLink class="btn btn-primary" to="/clientes/create">Criar</NuxtLink>
        <div class="table-responsive">
          <table class="table table-striped table-hover">
            <thead>
              <tr>
                <th>ID</th>
                <th>Nome</th>
                <th>Email</th>
                <th>Ações</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="(cliente, index) in clientes" :key="index">
                <td>{{ cliente.id }}</td>
                <td>{{ cliente.nome }}</td>
                <td>{{ cliente.email }}</td>
                <td>
                  <span class="btn btn-primary">Ver</span>
                  <span class="btn btn-warning">Editar</span>
                  <span class="btn btn-danger">Deletar</span>
                </td>
              </tr>
            </tbody>
          </table>

          <div>
            <span
              v-for="pageNumber in totalPages"
              :key="pageNumber"
              class="btn"
              :class="pageNumber == page ? 'btn-primary' : 'btn-default'"
              @click="updatePage(pageNumber)"
              >{{ pageNumber }}</span
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      clientes: [],
      page: 1,
      totalPages: 1,
      password: 'admin',
    }
  },
  mounted() {
    this.fetchClientes()
  },
  methods: {
    async fetchClientes() {
      const response = await this.$axios.get(`/clientes?page=${this.page}`)
      this.clientes = response.data.data
      this.totalPages = response.data.meta.last_page
    },
    async updatePage(selectedPage) {
      this.page = selectedPage
      await this.fetchClientes()
    },
  },
}
</script>
