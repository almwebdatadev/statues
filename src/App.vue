<template>
  <router-view></router-view>
</template>

<script>
export default {
  mounted() {
    const user = localStorage.getItem("user");
    if (!user) {
      this.$router.push("/login");
    }
  },
};
</script>

<style></style>
