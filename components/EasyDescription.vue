<template>
  <v-container>
    <v-row justify="center">
      <v-col class="text-center" cols="9">
        <h1>{{description}}</h1>
        <h4>Estoy seguro de esa descripcion un</h4>
        <v-progress-linear
          v-model="skillPorc"
          color="blue-grey"
          height="25"
          class="mt-8"
        >
        <strong>{{ skillPorc }}%</strong>
        </v-progress-linear>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  props: ['description', 'skill'],
  data: ()=>({
  }),
  computed:{
    skillPorc: function(){
      return (this.skill*100).toFixed(2);
    }
  },
}
</script>
