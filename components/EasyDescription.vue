<template>
  <v-container>
    <v-row justify="center" dense>
      <v-col class="text-center" cols="12" md="9" lg="5">
        <v-card>
          <v-img
            :src="url"
            class="white--text align-end"
            gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
            height="300px"
          >
            <v-card-title v-text="description"></v-card-title>
          </v-img>

          <v-progress-linear
            v-model="skillPorc"
            color="blue-grey"
            height="25"
            class="mt-8"
          >
            <strong>Estoy seguro un {{ skillPorc }}%</strong>
          </v-progress-linear>

        </v-card>
<!--        <h1>{{description}}</h1>
        <h4>Estoy seguro de esa descripcion un</h4>
        -->
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  props: ['description', 'skill', 'url'],
  data: ()=>({
  }),
  computed:{
    skillPorc: function(){
      return (this.skill*100).toFixed(2);
    }
  },
}
</script>
