<!-- eslint-disable max-len -->
<template>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Navbar</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <router-link to="/dashboard/products" class="nav-link">產品</router-link>
        <router-link to="/dashboard/orders" class="nav-link">訂單</router-link>
        <router-link to="/dashboard/coupons" class="nav-link">優惠券</router-link>
        <a class="nav-link" href="#" @click.prevent="logout">登出</a>
      </ul>
    </div>
  </div>
</nav>
</template>

<script>
export default {
  methods: {
    logout() {
      const api = `${process.env.VUE_APP_API}logout`;
      this.$http.post(api, this.user)
        .then((res) => {
          if (res.data.success) {
            this.$router.push('/login');
          }
        });
    },
    productlink() {
      this.$router.push('/dashboard/products');
    },
  },
};
</script>
