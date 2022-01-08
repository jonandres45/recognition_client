<template>
  <v-container>
    <v-row>
      <v-col cols="12" class="mt-8 text-center">
        <h3>Tags </h3>
        <v-progress-circular
          :rotate="360"
          :size="230"
          :width="18"
          :value="(tag.confidence * 100).toFixed(2)"
          :color="colors[Math.floor(Math.random() * (11-1) + 1)]"
          v-for="(tag, i) in tags"
          :key="i"
        >
          {{ tag.name }}
          {{ (tag.confidence * 100).toFixed(2) }} %
        </v-progress-circular>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
export default {
  props:['colors', 'tags']
}
</script>

<style scoped>
.v-progress-circular {
  margin: 2rem;
}
</style>
