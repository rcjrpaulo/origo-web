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
                  <NuxtLink
                    class="btn btn-primary"
                    :to="`/clientes/${cliente.id}/show`"
                  >
                    Ver
                  </NuxtLink>
                  <NuxtLink
                    class="btn btn-warning"
                    :to="`/clientes/${cliente.id}/edit`"
                  >
                    Editar
                  </NuxtLink>

                  <span
                    v-if="cliente.pode_deletar"
                    class="btn btn-danger"
                    @click="deleteUser(cliente.id)"
                  >
                    Deletar
                  </span>
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
    async deleteUser(id) {
      try {
        await this.$axios.delete(`/clientes/${id}`)
        await this.fetchClientes()
      } catch (err) {
        if (err.response.data.errors && err.response.data.errors.length) {
          for (const error of err.response.data.errors) {
            this.$swal.fire('Erro !', error, 'error')
          }
        }
      }
    },
  },
}
</script>
