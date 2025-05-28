<template>
  <button 
    class="cell"
    :class="{ revealed, flagged }"
    @click="handleClick"
    @contextmenu.prevent="toggleFlag"
  >
    <span v-if="flagged">🏠</span>
    <span v-else-if="revealed && mine">🐱</span>
    <span 
      v-else-if="revealed && value !== 0" 
      :data-value="value"
    >
      {{ value }}
    </span>
  </button>
</template>

<script>
export default {
  props: ['value', 'revealed', 'flagged', 'x', 'y', 'main'],
  methods: {
    handleClick() {
      this.$emit('reveal', this.x, this.y);
    },
    toggleFlag() {
      this.$emit('flag', this.x, this.y);
    }
  }
};
</script>