<template>
  <div>
    <Titulo></Titulo>
    <v-container fluid class="background-app">
      <v-row justify="center">
        <v-col cols="12" md="6">
          <v-file-input
            :rules="rules"
            accept="image/png, image/jpeg"
            placeholder="Pick an avatar"
            prepend-icon="mdi-camera"
            show-size
            v-model="obj.img"
            label="Avatar"
            @change="preview()"
          ></v-file-input>
        </v-col>
      </v-row>
      <v-row justify="center">
        <v-col cols="5">
          <v-img
            max-height="250"
            max-width="350"
            class="mx-auto"
            :src="obj.url"
          ></v-img>
        </v-col>
        <v-col cols="5" v-if="obj.url !== ''">
          <h3>Opciones de analisis</h3>
          <v-checkbox
            v-model="obj.op"
            label="Easy descripción"
            color="indigo"
            value="description"
            disabled
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Adivinar de que se trata la imagen"
            color="indigo"
            value="labels"
            @change="ejemplo()"
          ></v-checkbox>
          <v-btn color="success" outlined>Comenzar -></v-btn>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
export default{
  data: ()=>({
    obj: {
      img: null,
      op:['description'],
      url: '',
    },
    rules: [
      value => !value || value.size < 2000000 || 'Avatar size should be less than 2 MB!',
    ],
  }),
  methods:{
    preview(){
      if(this.obj.img != null){
        this.obj.url= URL.createObjectURL(this.obj.img);
        console.log(this.obj.url);
      }else{
        this.obj.url= '';
      }
    },
    async ejemplo(){
      const ip = await this.$axios.$post('http://localhost:5000/api/recognition/description', this.obj);
    }
  }
}
</script>

<style scoped>
.background-app{

}
</style>
