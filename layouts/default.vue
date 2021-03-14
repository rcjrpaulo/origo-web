<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">origo-web</a>
        <div id="navbarSupportedContent" class="collapse navbar-collapse">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li v-if="$auth.loggedIn" class="nav-item">
              <span class="nav-link"
                ><b>Olá, {{ $auth.user.name }}</b></span
              >
            </li>
            <li class="nav-item">
              <NuxtLink class="nav-link" to="/">Home</NuxtLink>
            </li>

            <li>
              <NuxtLink v-if="!$auth.loggedIn" class="nav-link" to="/login"
                >Login</NuxtLink
              >
            </li>

            <li>
              <a
                v-if="$auth.loggedIn"
                href="javascript:void(0)"
                class="nav-link"
                @click="logout($auth)"
                >Logout</a
              >
            </li>

            <li>
              <NuxtLink v-if="!$auth.loggedIn" class="nav-link" to="/register"
                >Cadastrar</NuxtLink
              >
            </li>

            <li>
              <NuxtLink v-if="$auth.loggedIn" class="nav-link" to="/clientes"
                >Clientes</NuxtLink
              >
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <Nuxt />
  </div>
</template>

<script>
export default {
  methods: {
    async logout(auth) {
      await auth.logout()
      location.reload()
    },
  },
}
</script>
