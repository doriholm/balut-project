<template>
  <div>
    <div class="container-fluid flex-center">
      <button class="btn btn-primary " v-on:click="clearLocalStorage">New Game</button>
    </div>
    <section class="section-scorecard flex-center container-fluid">
      <BalutCard/>
    </section>
  </div>
</template>

<script>
// @ is an alias to /src
import BalutCard from '@/components/BalutCard.vue'

export default {
  name: 'Home',
  components: {
    BalutCard
  },
  methods:{
    clearLocalStorage: function(){
            if(window.confirm('Do you want to start a new game?')){
                localStorage.removeItem('categories-values');
                location.reload();
                console.log('test');
            }
        }
  }
}
</script>

<style lang="scss" scoped>
.flex-center {
  display: flex;
  justify-content: center;
  padding: 2rem 0 ;
}


</style>