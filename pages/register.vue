<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Registrar</div>
      <div class="card-body">
        <form action="#">
          <p class="card-text">Faça seu cadastro</p>

          <input v-model="name" type="text" class="form-control" />
          <input v-model="email" type="text" class="form-control" />
          <input v-model="password" type="password" class="form-control" />

          <span class="btn btn-primary mt-3" @click="register">Cadastrar</span>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  auth: false,
  data() {
    return {
      name: 'teste',
      email: 'teste@teste.com',
      password: 'admin',
    }
  },
  mounted() {},
  methods: {
    async register() {
      try {
        await this.$axios.post('/auth/register', {
          name: this.name,
          email: this.email,
          password: this.password,
        })

        const data = {
          email: this.email,
          password: this.password,
        }

        await this.$auth.loginWith('local', {
          data,
        })

        this.$router.push('/clientes')
      } catch (err) {
        const statusCode = err.response.status || '500'
        const message = err.response.data.error || 'Houve um erro inesperado'
        this.$swal.fire(`Erro ${statusCode}`, message, 'error')
      }
    },
  },
}
</script>
