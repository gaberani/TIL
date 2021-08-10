<template>
  <div id="app">
    <test-mixin-vue-life-cycle></test-mixin-vue-life-cycle>
    <test-extends-vue-life-cycle></test-extends-vue-life-cycle>

    <!-- <template id="test-mixin-vue-life-cycle">
      <h1>
        Mixin
      </h1>
    </template>
    <template id="test-extends-vue-life-cycle">
      <h1>
        Extends
      </h1>
    </template> -->
  </div>
</template>

<script>
import Vue from 'vue'

const mixin1 = {
  created() {
    console.log('Created Mixin 1');
  }
}

const mixin2 = {
  created() {
    console.log('Created Mixin 2');
  }
}

const mixin3 = {
  mounted() {
    console.log('Mounted Mixin 3');
  }
}

const mixin4 = {
  mounted() {
    console.log('Mounted Mixin 4');
  }
}

Vue.component('test-mixin-vue-life-cycle', {
  template: "#test-mixin-vue-life-cycle",
  mixins: [mixin1, mixin2, mixin3, mixin4],
  created() {
    console.log('Created from the child!');
  },
  mounted() {
    console.log('Mounted from the child!');
  },
  render() {
    return (
      <template id="test-mixin-vue-life-cycle">
        <h1>
          Mixin
        </h1>
      </template>
    )
  }
})

/*
	Extends 
*/
const ParentComponent = {
  created() {
    console.log('[Extends] Created from the parent!');
  }
}

Vue.component('test-extends-vue-life-cycle', {
	template: "#test-extends-vue-life-cycle",
  extends: ParentComponent,
  created() {
    console.log('[Extends] Created from the child!');
  },
  render() {
    return (
      <template id="test-extends-vue-life-cycle">
        <h1>
          Extends
        </h1>
      </template>
    )
  }
})

export default {
  name: 'App',
  components: {
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
