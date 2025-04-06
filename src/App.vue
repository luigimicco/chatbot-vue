<script>
import ChatBot from './components/ChatBot.vue'
export default {
  components: { ChatBot },
  data() {
      return {
        chatBot: false,
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
      };
    },
}
</script>

<template>
  <ChatBot v-model="chatBot" :config="config" />
</template>

<style scoped>

</style>
