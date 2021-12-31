<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12">
        <v-card>
          <v-card-title>
            Encontre land marks
          </v-card-title>
          <v-card-text>
            <v-progress-linear
              :value="(obj.confidence * 100).toFixed(2)"
              height="25"
              class="mt-8"
              :color="colors[Math.floor(Math.random() * (11-1) + 1)]"
              v-for="(obj, i) in landmarks"
              :key="i"
            >
              <strong>Estoy seguro un {{obj.name}} {{(obj.confidence * 100).toFixed(2)}} %</strong>
            </v-progress-linear>
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
