<template>
  <div>
    <Titulo v-if="active"/>
    <v-container fluid class="background-app" v-if="active">
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
<!--          <v-checkbox
            v-model="obj.op"
            label="Detects Objects"
            color="purple"
            value="objects"
          ></v-checkbox>-->
          <v-checkbox
            v-model="obj.op"
            label="Detects faces and objects"
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
      :url = "obj.url"
      v-if="description !== null"
      class="mt-15"
    />

    <Tags
      :colors="colors"
      :tags="tags"
      v-if="tags !== null"
    />

    <FacesAndObjects
      :url="obj.url"
      :findObject="findObject"
      :findFaces="findFaces"
      :colors = "colors"
      :yesPrintObj="yesPrintObj"
      v-show="yesPrintObj"
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

    <v-container>
      <v-row>
        <v-col cols="12" class="text-center">
          <v-btn
            color="success"
            large
            outlined
            x-large
            @click="cleanAll()"
            v-if="!active"
          >
            <-- Subir otra foto
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
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
      '#4b7836',
      '#5489df',
      '#a23538',
      '#fe94f6',
      '#3b6965',
      '#5a9d98',
      '#ed8a4a',
      '#465d65',
      '#818181',
      '#baae36',
      '#663990'
    ],
    tags:null,
    description: null,
    findObject: null,
    findFaces: null,
    yesPrintObj: false,
    landmarks: null,
    isAdultContent: null,
    active: true,
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
      this.description = null;
      this.skill = '';
      this.tags = null;
      this.yesPrintObj = false;
      this.landmarks = null;
      this.isAdultContent = null;
      this.active= true;
      this.findObject = null;
      this.findFaces = null;
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
      this.active = false;
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
          this.findFaces = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/faces');
          console.log("Buscando objetos");
          this.findObject = await this.$axios.$get('https://recognition-jonandres.herokuapp.com/api/recognition/objects');
          console.log(this.findObject)
          console.log(this.findFaces);
          this.yesPrintObj = true;
          break;
        case "objects":
          this.yesPrintObj = true;
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

