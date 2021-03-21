<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Login</div>
      <div class="card-body">
        <form action="#" @submit.prevent="login">
          <p class="card-text">Entre com suas credenciais</p>

          <input
            v-model="email"
            type="email"
            required="required"
            class="form-control"
          />
          <input
            v-model="password"
            type="password"
            required="required"
            class="form-control"
          />

          <button class="btn btn-primary mt-3" type="submit">
            Fazer Login
          </button>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  auth: 'guest',
  data() {
    return {
      email: 'admin@admin.com',
      password: 'admin',
    }
  },
  methods: {
    async login() {
      const data = {
        email: this.email,
        password: this.password,
      }

      try {
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
