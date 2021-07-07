<template>
  <div class="die" :aria-label="getDieLabel()" v-if="rollNumber >= 1">
    {{ faces[rollNumber - 1] }}
  </div>
</template>

<script>
export default {
  name: 'Die',
  props: [ 'rollNumber' ],
  data() {
    return {
      faces: ['⚀','⚁','⚂','⚃','⚄','⚅']
    }
  },
  methods: {
    getDieLabel() {
      return `Die with face ${this.rollNumber + 1} showing`;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.die {
  font-size: 3rem;
}
</style>
