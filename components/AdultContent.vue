<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12">
        <v-alert
          prominent
          text
          :type="alert"
        >
          {{text}}
        </v-alert>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default {
  props: ['isAdultContent'],
  name: 'AdultContent',
  data: ()=>({
    alert: null,
    text: '',
  }),

  methods: {
    ejemplo(){
      if (this.isAdultContent !== null){
        if(this.isAdultContent.isAdultContent){
          this.alert='error';
          this.text='Parece que tu imagen tiene contenido para adultos';
        }else if(this.isAdultContent.isGoryContent){
          this.alert='error';
          this.text='Parece que tu imagen tiene contenido Gore';
        }else if(this.isAdultContent.isRacyContent){
          this.alert='warning';
          this.text='Parece que tu imagen tiene contenido erotico.';
        }else{
          this.alert='success';
          this.text='No hay ningún contenido para adultos/gore aquí :)'
        }
      }
    }
  },

}

</script>
