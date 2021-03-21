<template>
  <div class="container">
    <div class="card mt-3">
      <div class="card-header">Registrar</div>
      <div class="card-body">
        <form action="#" @submit.prevent="register">
          <p class="card-text">Faça seu cadastro</p>

          <input
            v-model="name"
            required="required"
            type="text"
            class="form-control"
            @invalid="mostraMsgDeErro('Nome obrigatório !')"
          />
          <input
            v-model="email"
            required="required"
            type="email"
            class="form-control"
            @invalid="mostraMsgDeErro('Email obrigatório !')"
          />
          <input
            v-model="password"
            required="required"
            type="password"
            class="form-control"
            @invalid="mostraMsgDeErro('Senha obrigatória !')"
          />

          <button type="submit" class="btn btn-primary mt-3">Cadastrar</button>
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
    mostraMsgDeErro(msg) {
      this.$swal.fire(`Erro`, msg, 'error')
    },
  },
}
</script>
