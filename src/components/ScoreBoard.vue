<template>
  <section class="score">
    Player <span>{{ winCount }}</span> x <span>{{ loseCount }}</span> Computer
  </section>
</template>

<script lang="ts">
export default {
  props: ["winCount", "loseCount"],
};
</script>

<style lang="scss">
section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;
}

section.score span {
  padding: 8px;
  font-weight: bold;
  border: 1px solid black;
}
</style>
