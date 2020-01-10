<template>
  <div id="app">
    <Navbar />
    <MainBody />
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'
import MainBody from '@/components/MainBody'

export default {
  components: {
    Navbar,
    MainBody
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #f4511e;
  background-color: #f8f9fa;
  width: 100vw;
  height: 100vh;
}
.text-light-orange {
  color: #ffa726;
}
.text-dark-orange {
  color: #f4511e;
}
</style>
