<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HomeWidget msg="Benford's law challange"/>
    
  </div>
</template>

<script>
// @ is an alias to /src
import HomeWidget from '@/components/HomeWidget.vue'

export default {
  name: 'Home',
  components: {
    HomeWidget
  }
}
</script>
