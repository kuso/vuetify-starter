<template>
  <div class="hello_world">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue';

export default {
  name: 'hello_world',
  components: {
    HelloWorld,
  },
};
</script>
