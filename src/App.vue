<script>
import ChatBot from './components/ChatBot.vue'
export default {
components: { ChatBot },
data() {
    return {
      modalChatBot: false,
      config: {
        assets: "../assets/chatbot/",
        avatar: "bot.png",
        beep: "beep.mp3",
        hystory: false,
        answers : {
          'welcome' :
            {
              text : "Ciao! Sono l'assistente virtuale del portfolio di <strong>Luigi Micco</strong>. Posso aiutarti a trovare informazioni su questo sito. Come posso esserti utile?",
            },
          'default' :
            {
              text : "Mi dispiace, non ho informazioni specifiche su questo argomento. Prova a chiedermi dei progetti, competenze, esperienze o contatti.",
            }, 
          'no' :
            {
              token: ['no'],
              text : ["Va bene! Se ti posso aiutare in altro, fammelo sapere!", "Nessun problema! Sono qui per aiutarti con qualsiasi altra informazione!", "Come preferisci!", "Capito! Se cambi idea o vuoi vedere altro, sono qui!"],
            }, 
          'bot' :
            {
              token: ['chiami', 'sei'],
              text : "Sono <b>Botty</b>, un chatbot creato per aiutarti a navigare in questo sito. Posso fornirti informazioni su progetti, competenze ed esperienze. Non esitare a chiedermi qualsiasi cosa sul portfolio!",
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
  <ChatBot v-model="modalChatBot" :config="config" />
</template>

<style scoped>

</style>
