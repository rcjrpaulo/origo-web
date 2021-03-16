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
    deleteUser(id) {
      this.$swal
        .fire({
          title: 'Tem certeza ?',
          text: 'Você está preste a deletar um cliente',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Sim, deletar !',
          cancelButtonText: 'Não',
        })
        .then(async (result) => {
          if (result.isConfirmed) {
            try {
              await this.$axios.delete(`/clientes/${id}`)
              await this.fetchClientes()

              this.$swal.fire(
                'Sucesso !',
                'O cliente foi deletado com sucesso !',
                'success'
              )
            } catch (err) {
              const statusCode = err.response.status || '500'
              const message =
                err.response.data.error || 'Houve um erro inesperado'
              this.$swal.fire(`Erro ${statusCode}`, message, 'error')
            }
          }
        })
    },
  },
}
</script>
