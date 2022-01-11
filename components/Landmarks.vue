<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" class="text-center">
        <v-card>
          <v-card-title>
            i finded land marks
          </v-card-title>
          <v-card-text>
            <v-chip
              class="mt-8"
              :color="colors[Math.floor(Math.random() * (11-1) + 1)]"
              v-for="(obj, i) in landmarks"
              :key="i"
            >
              <strong>{{obj.name}} {{(obj.confidence * 100).toFixed(2)}} %</strong>
            </v-chip>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>

export default {
  props: ['landmarks', 'colors'],
  data: ()=>({

  }),
}

</script>
