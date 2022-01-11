<template>
  <v-container class="mt-16">
    <v-row justify="center">
      <v-col cols="6" class="text-center" v-if="findObject">
        <h2>Estoy un % seguro de que hay en la imagen...</h2>
        <v-card>
          <v-card-text>
            <v-chip
              :color="object.color"
              v-for="(object, i) in objects"
              :key="i"
            >
              <strong>{{object.element.object}} {{(object.element.confidence * 100).toFixed(2)}} %</strong>
            </v-chip>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" class="text-center" v-if="faces.length > 0">
        <h1>Y encontre {{faces.length }} cara{{faces.length > 1 ? 'S' : ''}} </h1>
        <h2
          class="mt-8"
          v-for="(obj, i) in faces"
          :key="'cara'+i"
        >
          <v-chip
            class="ma-2"
            :color="obj.color"
          >
            Genero: {{obj.element.gender}} Posible edad: {{obj.element.age}}
          </v-chip>
        </h2>
        <h2>Te los muestro todo en la siguiente imagen que e coloreado para ti :) </h2>
      </v-col>
      <v-col v-else cols="12" class="text-center">
        <h2>No encontre ningún rostro</h2>
      </v-col>
      <v-col cols="12" class="text-center">
<!--        <div style="width: 100%; height: 50vh; overflow: scroll">-->
          <canvas id="myCanvas" style="border:1px solid">
            Your browser does not support the HTML canvas tag.
          </canvas>
<!--        </div>-->
        <img :src="url" alt="" id="image" style="display: none">
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default{
  props: ['url', 'colors', 'yesPrintObj', 'findObject', 'findFaces'],

  data: ()=> ({
    ejemplo: [
      {
        rectangle:
          {h: 153, w:112, x: 114, y: 130},
        object: 'monitor',
        confidence: .545,
        parent:
          {object: 'display', confidence: .68}}
    ],
    objects:[],
    faces:[]
  }),
  mounted() {

  },
  methods:{
    start(){
      this.objects();
    },
    setImage(name){
      let img = document.getElementById("image");
      let heightImage = img.height * .50,
        widthImage = img.width * .50;
      let c = document.getElementById(name);
      c.height = heightImage;
      c.width = widthImage;
      let ctx = c.getContext("2d");
      ctx.drawImage(img, 0, 0, widthImage, heightImage);
      ctx.lineWidth = 3;
      ctx.strokeStyle = "red";

      return ctx;

    },
    createRectObj(element, ctx){
      ctx.beginPath();
      let color = this.colors[Math.floor(Math.random() * (11-1) + 1)];
      console.log("Color: " + color);
      ctx.strokeStyle = color;
      this.objects.push({element: element, color: color});
      ctx.rect(element.rectangle.x*.50, element.rectangle.y*.50, element.rectangle.w*.50, element.rectangle.h*.50);
      ctx.stroke();
    },
    cresteReactFac(element, ctx){
      ctx.beginPath();
      let color = this.colors[Math.floor(Math.random() * (11-1) + 1)];
      ctx.strokeStyle = color;
      this.faces.push({element: element, color: color});
      ctx.rect(element.faceRectangle.left *.50, element.faceRectangle.top *.50, element.faceRectangle.width *.50, element.faceRectangle.height *.50)
      ctx.stroke();
    }
  },
  watch:{
    yesPrintObj: function() {
        this.objects = [];
        this.faces = [];
        let ctx = this.setImage('myCanvas');
        if(this.findObject){
          this.findObject.forEach((element)=> this.createRectObj(element, ctx));
        }
        if(this.findFaces){
          this.findFaces.forEach((element)=>{this.cresteReactFac(element, ctx)});
        }
    },
  }
}

</script>
