<template>
    <b-navbar :fixed-top="true">
        <template #brand>
            <b-navbar-item tag="router-link" :to="{ path: '/' }">
                <h1><b>LOGO</b></h1>
            </b-navbar-item>
        </template>
        <template #start>
            <b-navbar-item href="/">
                Review Users
            </b-navbar-item>
            <b-navbar-item href="myreview">
                Your Reviews
            </b-navbar-item>
        </template>
    </b-navbar>
</template>

<script>
export default {
  data() {
    return {
      mobile: "reduce",
      reduce: false
    };
  }
};
</script>
