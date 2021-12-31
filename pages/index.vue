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
            @change="preview"
          ></v-file-input>
        </v-col>
      </v-row>
      <v-row justify="center">
        <v-col cols="5">
          <v-img
            max-height="350"
            max-width="350"
            class="mx-auto"
            :src="obj.url"
          ></v-img>
        </v-col>
        <v-col cols="5" v-if="obj.url !== ''">
          <h3>analysis options</h3>
          <v-checkbox
            v-model="obj.op"
            label="Easy descripción"
            color="indigo"
            value="description"
            disabled
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Detects tags"
            color="pink"
            value="labels"
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Detects Objects"
            color="purple"
            value="objects"
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Detects faces"
            color="blue"
            value="faces"
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Detects landmarks"
            color="yellow"
            value="celebrities"
          ></v-checkbox>
          <v-checkbox
            v-model="obj.op"
            label="Detects Adult Content"
            color="indigo"
            value="adult"
          ></v-checkbox>
          <v-btn
            color="success"
            outlined
            @click="sendImage()"
            :loading="load"
          >
            let's go :D ->
          </v-btn>
        </v-col>
        <v-col cols="5">
          <h3>Litle description</h3>
          <p>{{description}}</p>
          <h3>Estoy seguro de esa descripcion un</h3>
          <v-progress-linear
            v-model="skillPorc"
            color="blue-grey"
            height="25"
            class="mt-8"
          >
            <strong>{{ skillPorc }}%</strong>
          </v-progress-linear>
        </v-col>
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
        <v-col cols="12" class="text-center">
          <h1>Is adult content, gore or racy?</h1>
          <h1 color="red">Yes o.o</h1>
        </v-col>
      </v-row>
    </v-container>
    <FacesAndObjects
      :url="obj.url"
      :findObjectAndFaces="findObjectAndFaces"
      :colors = "colors"
      :yesPrint="yesPrint"
    ></FacesAndObjects>
    <Landmarks
      :landmarks="landmarks"
      :colors = "colors"
    />
  </div>
</template>

<script>

export default{
  name:"principal",
  data: ()=>({
    value: 50,
    skill: 0,
    load: false,
    obj: {
      img: null,
      op:[],
      url: '',
    },
    rules: [
      value => !value || value.size < 2000000 || 'Avatar size should be less than 2 MB!',
    ],
    colors:[
      '',
      'success',
      'primary',
      'error',
      'pink',
      'cyan',
      'teal',
      'orange',
      'blue-grey',
      'grey',
      'amber',
      'deep-purple'
    ],
    tags:null,
    description: '',
    findObjectAndFaces:{},
    yesPrint: false,
    landmarks: {},

  }),
  mounted() {

  },
  computed:{
    skillPorc: function(){
      return (this.skill*100).toFixed(2);
    }
  },
  methods:{
    preview(event){
      if(this.obj.img != null){
        this.obj.url= URL.createObjectURL(this.obj.img);
      }else{
        this.obj.url= '';
      }
    },
    async sendImage(){
      this.load = true;
      let data = new FormData();
      data.append('img', this.obj.img);
//      data.append('body', this.obj);
      try {
        const res = await this.$axios.$post('https://recognition-jonandres.herokuapp.com/api/recognition/upload-image', data);

        if(res){
          this.obj.op.forEach(element => this.services(element));
        }
        this.load = false;
      }catch(e){
        console.log("Error:" + e.message);
      }
    },
    async services(data){
      switch(data){
        case "description":
          console.log("Aqui vamos a ver la descripcion");
          const descriptionObj = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/description');
          this.description = descriptionObj.text;
          this.skill = descriptionObj.confidence;
          break;
        case "labels":
          console.log("Aqui vamos a ver las etiquetas");
          const tagsObj = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/labels');
          this.tags = tagsObj;
          break;
        case "faces":
          console.log("Buscando las caras");
          this.findObjectAndFaces.facesArray = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/faces');
          this.yesPrint = true;
          console.log(this.findObjectAndFaces.facesArray);
          break;
        case "objects":
          console.log("Buscando objetos");
          this.findObjectAndFaces.objectsArray = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/objects');
          this.yesPrint = true;
          console.log(this.findObjectAndFaces.objectsArray)
          break;
        case "celebrities":
          console.log("Buscando celebridades")
          this.landmarks = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/landmark');
          console.log(this.landmarks);
          break;
        case "adult":
          console.log("Buscando contenido de adultos")
          console.log(await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/content-adult'));
      }
    }
  },
}
</script>

<style scoped>
.v-progress-circular {
  margin: 1rem;
}
</style>
