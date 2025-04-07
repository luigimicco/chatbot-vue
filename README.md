# Botty

Botty is a Vue3 component that allows you to insert a virtual assistant on your site.

Botty is easily configurable

## How to use

```
<script>
import ChatBot from '...../ChatBot.vue'

export default {
  components: { ChatBot },
  data() {
    return {
      chatBot: false,
      config: { .....
      },
      theme : { .....
      }      
    };
  },
}
</script>

<template>
  <ChatBot v-model="chatBot" :config="config" :theme="theme"  />
</template>

<style scoped>

</style>

```

## Configuration

This is a sample configuration:

```
config: {
  name: "Botty",
  assets: "../assets/chatbot/",
  avatar: "bot.png",
  beep: "beep.mp3",
  hystory: false,    // true|false 
  position: 'right', // "left|center|right"
  sound: false,      // true|false  
  answers : {
    'welcome' :
      {
        text : "Ciao! Sono l'assistente virtuale. Posso aiutarti a trovare informazioni su questo sito. Come posso esserti utile? Prova a chiedermi qualcosa o usa le opzioni che ti propongo.",
        options: [
                    {
                      type: "text",
                      text : "Esempi di risposte" ,
                      action: {
                          type: "answer",
                          answer: "es_esempi",
                        }
                    },
                  ]            

      },
    'default' :
      {
        text : "Mi dispiace, non sono in grado di capire la tua domanda. Prova a chiedermi qualcos'altro.",
      },

    'es_esempi' :
      {
        token: ['esempio', 'esempi'],
        options: [
                    {
                      type: "text",
                      text : "Testo semplice" ,
                      action: {
                          type: "text",
                          text: "bla bla ...",
                        }
                    },
                    {
                      type: "text",
                      text : "Risposta multipla" ,
                      action: {
                          type: "answer",
                          answer: "es_multi",
                        }
                    },                          
                    {
                      type: "text",
                      text : "Altra risposta" ,
                      action: {
                          type: "answer",
                          answer: "es_img",
                        }
                    },
                    {
                      type: "text",
                      text : "Vai a google.it" ,
                      action: {
                          type: "url",
                          url: "https://www.google.it",
                        }
                    },                          
                  ]                 
      },


    'es_img' :
      {
        text : "Esempio di risposta con imagine allegata.",
        options: [
                    {
                      type: "img",
                      img : "bot.png" ,
                    },
                  ]                 
      },
      
    'es_multi':
      {
        text : "Esempio di risposta multipla.|Con testo diviso|In più parti",          
      },

    'no' :
      {
        token: ['fine'],
        text : ["Va bene! Se ti posso aiutare in altro, fammelo sapere!", "Nessun problema! Sono qui per aiutarti con qualsiasi altra informazione!", "Come preferisci!", "Capito! Se cambi idea o vuoi vedere altro, sono qui!"],
      }, 
    'bot' :
      {
        token: ['chiami', 'sei'],
        text : "Sono <b>Botty</b>, un chatbot creato per aiutarti a navigare in questo sito. Posso fornirti informazioni su progetti, competenze ed esperienze. Non esitare a chiedermi qualsiasi cosa sul portfolio!",
        options: [
                    {
                      type: "img",
                      img : "bot.png" ,
                    },
                  ]                
      },          
    'ans_progetti' :
      {
        token: ['progetti', 'prova'],
        text : "testo dei progetti",
        options: [
                    {
                      type: "text",
                      text : "Si" ,
                      action: {
                          type: "text",
                          text: "bla bla ...",
                        }
                    },
                    {
                      type: "text",
                      text : "No" ,
                      action: {
                          type: "text",
                          text: "bla bla ...",
                        }
                    },
                  ]            

      },          
    'ans_goto' :
      {
        token: ['pagina', 'vai'],
        text : "Vuoi che ti porti ad una pagina specifica ?",
        options: [
                    {
                      type: "text",
                      text : "Blog" ,
                      action: {
                          type: "url",
                          url: "bla bla ...",
                        }
                    },
                    {
                      type: "text",
                      text : "Contatti" ,
                      action: {
                          type: "url",
                          text: "bla bla ...",
                        }
                    },
                  ]            

      },          
  }
}
```

## Default theme (optional)

```
theme : {
    icon_label_color : "#fff",
    icon_label_background : "#0084ff",
    icon_label_shadow : "#0003",
    icon_border: "#8d8d8d",
    icon_border_hover: "#0069d9",
    icon_shadow: "#00000040",
    chatbot_background: "#fff",
    chatbot_shadow: "#00000040",

    header_title: "#fff",
    header_gradient_start: "#008cff",
    header_gradient_end: "#000b19",
    action_button: "#fff",
    action_button_background_hover: "#fff3",

    messages_background: "#ffffec",
    bot_message_background:  "#e8f0fe",
    user_message_background: "#cbffe9",

    bot_message: "#222",
    user_message: "#222",
    message_shadow: "#00000014",
    message_border: "#d0d7e0",
    message_time: "#666",      
    typing_backround: "#888",

    option: "#01234c",
    option_background: "#d8d8d9",
    option_background_hover: "#e1e4e8",

    footer_backround: "#fff",
    footer_border: "#e9ecef",

    input: "#333",
    input_hint: "#888",
    input_backround: "#f1f3f4",

    send: "#fff",
    send_backround: "#03346e",
    send_backround_hover: "#0253a4",

}
```

## Play
```
npm run dev
```
build the library and start the playground