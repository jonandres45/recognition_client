<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" class="text-center" v-if="findObjectAndFaces.objectsArray">
        <v-card>
          <v-card-title>
            Estoy un % seguro de que hay en la imagen...
          </v-card-title>
          <v-card-text>
            <v-progress-linear
              :value="(obj.confidence * 100).toFixed(2)"
              height="25"
              class="mt-8"
              :color="colors[Math.floor(Math.random() * (11-1) + 1)]"
              v-for="(obj, i) in findObjectAndFaces.objectsArray"
              :key="i"
            >
              <strong>{{obj.object}} {{(obj.confidence * 100).toFixed(2)}} %</strong>
            </v-progress-linear>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" class="text-center" v-if="findObjectAndFaces.facesArray">
        <h1>Y encontre {{findObjectAndFaces.facesArray.length }} caras </h1>
        <p
          class="mt-8"
          v-for="(obj, i) in findObjectAndFaces.facesArray"
          :key="'cara'+i"
        >
          <strong>Genero: {{obj.gender}} Posible edad: {{obj.age}} </strong>
        </p>
        <h2>Te los muestro todo en la siguiente imagen que e coloreado para ti :) </h2>
      </v-col>
      <v-col cols="10">
        <canvas id="myCanvas" style="border:1px solid">
          Your browser does not support the HTML canvas tag.
        </canvas>
        <img :src="url" alt="" id="image" style="display: none">
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default{
  props: ['url', 'colors', 'findObjectAndFaces', 'yesPrint'],

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
  }),
  mounted() {

  },
  methods:{
    start(){
      this.objects();
    },
    objects(ctx){
      //x y, w, h
      this.findObjectAndFaces.objectsArray.forEach(element => ctx.rect(element.rectangle.x, element.rectangle.y, element.rectangle.w, element.rectangle.h));
      ctx.stroke();
    },
    faces(ctx){
      this.findObjectAndFaces.facesArray.forEach(element => ctx.rect(element.faceRectangle.left, element.faceRectangle.top, element.faceRectangle.width, element.faceRectangle.height));
      ctx.stroke();
    }
  },
  watch:{
    yesPrint: function() {
        let img = document.getElementById("image");
        let heightImage = img.height,
            widthImage = img.width;
        let c = document.getElementById("myCanvas");
        c.height = heightImage;
        c.width = widthImage;
        let ctx = c.getContext("2d");
        ctx.drawImage(img, 0, 0);
        ctx.lineWidth = 2;
        ctx.strokeStyle = "red";

        if(this.findObjectAndFaces.objectsArray){
          this.objects(ctx);
        }
        if(this.findObjectAndFaces.facesArray){
          this.faces(ctx);
        }
    },
  }
}

</script>
