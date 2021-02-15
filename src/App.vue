<template>
  <Form2 />
</template>

<script>
import Form2 from "./components/Form2.vue"

export default {
  name: 'App',
  components: {
    Form2
  }
}
</script>

<style>

* {
  box-sizing: border-box;
}

body {
  font-family: Arial, Roboto, Oxygen, Fira Sans, Helvetica Neue, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-size: 12px;
  color: #2c3e50;
  background: #ececec;
}

hr {
  margin: 1rem 0;
}

strong,
.bold {
  font-weight: 500;
}

ol,
p,
ul {
  line-height: 1.7;
}

a {
  color: #3eaf7c;
  font-weight: 500;
  text-decoration: none;
}

h1,
h2,
h3,
h4,
h5,
h6 {
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.45;
}

h1 {
  font-weight: 700;
}

</style>
