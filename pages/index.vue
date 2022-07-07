<template>
  <div>
    <Nav/>
    <nuxt-child></nuxt-child>
  </div>
</template>

<script>
import Nav from "../components/navbar/nav.vue";

export default {
  name: "IndexPage",
  components: {
  Nav
  }
}
</script>
