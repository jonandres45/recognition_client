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
      </v-row>
    </v-container>

    <EasyDescription
      :description = "description"
      :skill = "skill"
      v-if="description !== ''"
    />

    <Tags
      :colors="colors"
      :tags="tags"
      v-if="tags !== null"
    />

    <FacesAndObjects
      :url="obj.url"
      :findObjectAndFaces="findObjectAndFaces"
      :colors = "colors"
      :yesPrintObj="yesPrintObj"
      :yesPrintFac="yesPrintfac"
      v-if="yesPrintObj || yesPrintfac"
    />

    <Landmarks
      :landmarks="landmarks"
      :colors = "colors"
      v-if="landmarks !== null"
    />
    <AdultContent
      :isAdultContent = "isAdultContent"
      v-if="isAdultContent !== null"
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
      op:['description'],
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
    yesPrintObj: false,
    yesPrintfac: false,
    landmarks: null,
    isAdultContent: null
  }),
  mounted() {

  },

  methods:{
    preview(event){
      if(this.obj.img != null){
        this.obj.url= URL.createObjectURL(this.obj.img);
      }else{
        this.obj.url= '';
        this.cleanAll();
      }
    },

    cleanAll(){
      this.description = '';
      this.skill = '';
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
          this.yesPrintObj = true;
          console.log(this.findObjectAndFaces.facesArray);
          break;
        case "objects":
          console.log("Buscando objetos");
          this.findObjectAndFaces.objectsArray = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/objects');
          this.yesPrintFac = true;
          console.log(this.findObjectAndFaces.objectsArray)
          break;
        case "celebrities":
          console.log("Buscando celebridades")
          this.landmarks = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/landmark');
          console.log(this.landmarks);
          break;
        case "adult":
          console.log("Buscando contenido de adultos")
          this.isAdultContent = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/content-adult');
          console.log(this.isAdultContent);
      }
    }
  },
}
</script>

