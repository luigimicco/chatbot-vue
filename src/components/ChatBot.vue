<template>
  <teleport to="body">
    <div  class="chatbot-container">
      <div v-if="!modelValue" class="chatbot-icon" :class="position" @click='$emit("update:modelValue", true)'   >
        <div class="chatbot-label">Botty</div>
        <img :src="avatar" alt="Icona Chatbot">
      </div>
      <div v-else class="chatbot-window " :class="position">
        <div  class="chatbot-header">
          <div  class="header-info">
            <img :src="avatar" alt="Assistente virtuale" class="header-avatar">
            <div  class="header-text">
              <h3 >Botty</h3>
            </div>
          </div>
          <div  class="header-actions">
            <button class="action-btn" @click="toggleSound()" >
              <span v-if="soundEnabled" class="action-icon">🔊</span>
              <span v-else class="action-icon">🔇</span>
            </button>
            <button  class="action-btn" @click='$emit("update:modelValue", false)'><span>✕</span></button>
          </div>
        </div>
        <div  class="chatbot-content">
          <div class="chatbot-messages" ref="messagesContainer">
            <div v-for="(message, index) in messages" >
              <div class="message" :class="message.sender">
                  <div v-if="message.sender == 'bot'">
                    <div class="message-content">
                      <div class="bot-avatar"><img  :src="avatar"
                        alt="Botty">
                      </div>
                      <div v-if="message.text" class="message-bubble">
                        <p v-html="message.text"></p>
                        <span  class="message-time">{{ message.time }}</span>
                      </div>
                    </div>

                    <div v-if="message.options" class="action-bubble">
                      <template v-for="option in message.options">
                        <span v-if="option.type" class="">
                          <button @click="doAction(option)" v-if="option.type == 'text'">{{ option.text }}</button>
                          <img @click="doAction(option)" v-if="option.type == 'img'" :src="getUrl(option.img)">
                        </span>
                      </template>
                      
                    </div>
                   
                  </div>

                  <div v-else class="message-content">
                    <div class="message-bubble">
                      <p >{{ message.text }}</p>
                      <span  class="message-time">{{ message.time }}</span>
                    </div>
                  </div>



              </div>
              <div class="message bot" v-if="isTyping && index == (messages.length-1)">
                <div  class="message-content">
                  <div class="bot-avatar"><img  :src="avatar"
                    alt="Assistente virtuale">
                  </div>
                  <div class="message-bubble">
                    <div class="typing-indicator"><span></span><span></span><span></span></div>
                  </div>
                </div>
              </div>

            </div>


          </div>
          <div  class="chatbot-input">
            <input v-model="currentMessage" @keyup.enter="userMessage"
              placeholder="digitare qui ..." type="text" aria-label="Messaggio per il chatbot">
              <button @click="userMessage" class="send-btn" aria-label="Invia messaggio"><span >➤</span></button>
          </div>
        </div>
      </div>
    </div>
  </teleport>
</template>

<script>
export default {
  props: {
        modelValue: {
            type: Boolean,
            required: true,
            default: false
        },
        config: {
            type: Object,
            required: true,
            default: {}
        },

  },
  data() {
    return {
      avatar: this.getUrl(this.config.avatar) ?? null,
      beep: this.getUrl(this.config.beep) ?? null,
      history: this.config.hystory ?? false,
      position: this.config.position ?? 'right',
      answers: this.config.answers,
      soundEnabled: this.config.sound ?? true,
      messages: [],

      currentMessage: "",
      isTyping: false,
      messageSound: null,
      smoothScrollInterval: null,
      smoothScrollSpeed: 1,
      smoothScrollDelay: 15,
    }
  },
  watch: {
      // whenever showFilter changes, this function will run
      modelValue(newValue, oldValue) {
          if (newValue != oldValue && newValue === true ) {
            this.$nextTick(() => {
              //this.startSmoothScroll();
              this.scrollToBottom(false)
            });

          }
      },
  },

  computed: { },
  created() {
    if (this.history)
      this.messages = this.getStorage('chatbot');
    else 
      this.clearStorage('chatbot');

    if (!this.messages.length) {
      this.messages = [{
        sender: "bot",
        text: this.answers['welcome'].text,
        options: this.answers['welcome'].options ?? null,
        time: this.getCurrentTime()
      }];
    }
  },
  mounted() {
      this.messageSound = new Audio(this.beep);
  },

  updated() {
    if (this.history) this.saveStorage('chatbot', this.messages);
  },
  methods: {
    saveStorage(key, data) {
        localStorage.setItem(key, JSON.stringify(data));
    },
    getStorage(key, item) {
        if( localStorage.getItem(key) && item) {
            const data = JSON.parse(localStorage.getItem(key))
            return data[item]
        }
        else if(localStorage.getItem(key)) {
          return JSON.parse(localStorage.getItem(key))
        } else {
          return JSON.parse("[]");
        }
    },
    clearStorage(key='false') {
        if(key) {
            localStorage.removeItem(key);
        } else {
            localStorage.clear();
        }
    },

    getUrl(name) {
      return new URL(this.config.assets + `${name}`, import.meta.url).href
    },
    doAction(option) {
      if (option.action) {
        let newMessage = null;

        if (option.action.type == 'text') {
          newMessage = {
            text: option.action.text,
          };

        } else if (option.action.type == 'answer') {
          newMessage = {
            options: this.answers[option.action.answer].options ?? null,
          };          
          let texts = this.answers[option.action.answer].text;
          if (Array.isArray(texts)) {
            newMessage.text = texts[Math.floor(Math.rand()* texts.length)];
          } else {
            newMessage.text = texts;
          }

        } else if (option.action.type == 'url') {


        }

        if (newMessage) {
          this.isTyping = true;
          this.$nextTick(() => {
            this.scrollToBottom(false);
          });
          setTimeout(() => {
            this.isTyping = false;
            newMessage.sender = "bot";
            newMessage.time = this.getCurrentTime();
            this.messages.push(newMessage);
            } , Math.floor(Math.random()*1500))
        }

      }
    },

    toggleSound() {
      this.soundEnabled = !this.soundEnabled;
    },

    playMessageSound() {
      this.soundEnabled && this.messageSound && this.messageSound.play().catch(e => console.error("Errore riproduzione audio:", e))
    },

    startSmoothScroll() {
      this.stopSmoothScroll();
      const e = this.$refs.messagesContainer;
      if (!e)
        return;
      const t = e.scrollHeight - e.clientHeight;
      this.smoothScrollInterval = setInterval(() => {
        if (e.scrollTop >= t) {
          this.stopSmoothScroll();
          return
        }
        const n = t - e.scrollTop;
        const s = Math.max(this.smoothScrollSpeed, n * .05);
        e.scrollTop += s;
        if (t - e.scrollTop < 1) {
          e.scrollTop = t;
          this.stopSmoothScroll();
        }
      }, this.smoothScrollDelay);
    },
    stopSmoothScroll() {
      if (this.smoothScrollInterval) {
        clearInterval(this.smoothScrollInterval);
        this.smoothScrollInterval = null;
      }
    },
    userMessage() {
      const e = this.currentMessage.trim();
      if (!e)
        return;

      this.messages.push({
        sender: "user",
        text: e,
        time: this.getCurrentTime()
      });
      this.currentMessage = "";
      this.$nextTick(() => {
        this.startSmoothScroll();
        //          this.scrollToBottom(false)
      });
      
      this.handleUserResponse(e.toLowerCase());

    },

    handleUserResponse(e) {
      this.isTyping = true;

      let words = e.split(/\W+/).filter(function(token) {
          token = token.toLowerCase();
          return token.length >= 2;
      });

      let result = "default";
      let found = false;
      const ansKeys = Object.keys(this.answers);
      for (let a = 0; a < ansKeys.length; a++) {
        const key = ansKeys[a];
        if (this.answers[key].token) {
          let tokens = this.answers[key].token;
          for (let index = 0; index < tokens.length; index++) {
            if (words.includes(tokens[index])) {
              result = key;
              found = true;
              break;
            }
          }
        }
        if (found) break;
      }

      this.isTyping = true;

      setTimeout(() => {
        this.isTyping = false;
        const newMessage = {
          sender: "bot",
          time: this.getCurrentTime(),
          options: this.answers[result].options ?? null,
        };     
        
        let texts = this.answers[result].text;
        if (Array.isArray(texts)) {
          newMessage.text = texts[Math.floor(Math.random()* texts.length)];
        } else {
          newMessage.text = texts;
        }

        this.messages.push(newMessage);
        this.playMessageSound();
        this.$nextTick(() => {
          this.startSmoothScroll();
//          this.scrollToBottom(false)
        });
        } , Math.floor(Math.random()*1500))

    },

    getCurrentTime() {
      const e = new Date;
      return `${e.getHours().toString().padStart(2, "0")}:${e.getMinutes().toString().padStart(2, "0")}`
    },
    scrollToBottom(e = false) {
      if (this.$refs.messagesContainer) {
        if (e) {
          this.startSmoothScroll()
         } else {
          this.stopSmoothScroll()
         };
        this.$refs.messagesContainer.scrollTop = this.$refs.messagesContainer.scrollHeight;
      }
    },
  }
}
</script>


<style scoped>
.chatbot-container {
  font-family: Segoe UI, Tahoma, Geneva, Verdana, sans-serif;
}
.chatbot-icon {
  position: fixed;
  bottom: 30px;
  cursor: pointer;
  z-index: 1000;
  transition: transform 0.3s ease, opacity 0.5s ease;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.chatbot-icon.right {
  right: 20px;
}
.chatbot-icon.left {
  left: 20px;
}

.chatbot-icon.center {
  left: calc((100% - 50px) / 2);
}

.chatbot-label {
  background-color: #0084ff;
  color: #fff;
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 14px;
  font-weight: 700;
  margin-bottom: 5px;
  box-shadow: 0 3px 6px #0003;
}
.chatbot-icon:hover {
  transform: scale(1.1);
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  70% {
    transform: scale(1.1);
  }
  to {
    transform: scale(1);
  }
}
.chatbot-icon img {
  width: 50px;
  height: 50px;
  object-fit: contain;
  border-radius: 50%;
  box-shadow: 0 4px 12px #00000040;
  border: 2px solid #8d8d8d;
  transition: border-color 0.3s ease;
}
.chatbot-icon:hover img {
  border-color: #0069d9;
}

.chatbot-window {
  position: fixed;
  bottom: 30px;
  right: 20px;
  width: 400px;
  height: 600px;
  max-height: 80vh;
  background-color: #fff;
  border-radius: 14px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  box-shadow: 0 10px 30px #00000040;
  z-index: 999;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.chatbot-window.right {
  right: 20px;
}

.chatbot-window.left {
  left: 20px;
}

.chatbot-window.center {
  left: calc((100% - 400px) / 2);
}

.chatbot-content {
  display: flex;
  flex-direction: column;
  flex: 1;
  overflow: hidden;
}
@media (max-width: 480px) {
  .chatbot-window {
    width: 90%;
    right: 5%;
  }
}
.chatbot-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 8px;
  background: linear-gradient(135deg, #008cff, #000b19);
  color: #fff;
  flex-shrink: 0;
}
.header-info {
  display: flex;
  align-items: center;
}
.header-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
  object-fit: contain;
}
.header-text h3 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
}
.status-online {
  font-size: 12px;
  opacity: 0.8;
}
.header-actions {
  display: flex;
  gap: 6px;
}
.action-btn {
  background: none;
  border: none;
  color: #fff;
  cursor: pointer;
  font-size: 16px;
  padding: 5px;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.2s;
}
.action-btn:hover {
  background-color: #fff3;
}
.action-icon {
  font-size: 14px;
}
.chatbot-messages {
  flex: 1;
  overflow-y: auto;
  padding: 16px;
  background-color: #ffffec;
}
.message {
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
}
.message-content {
  display: flex;
  align-items: flex-start;
  max-width: 100%;
}
.bot {
  align-items: flex-start;
}
.bot .message-content {
  margin-right: auto;
}
.user {
  align-items: flex-end;
}
.user .message-content {
  margin-left: auto;
  flex-direction: row-reverse;
}

.bot-avatar {
  width: 40px;
  height: 40px;
  margin-right: 8px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
}

.bot-avatar img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 50%;
}
.message-bubble {
  padding: 12px 16px;
  border-radius: 18px;
  box-shadow: 0 2px 6px #00000014;
  position: relative;
  min-width: 40px;
  display: inline-block;
  border: 1px solid #d0d7e0;
  color: #222;
}

.bot .message-bubble {
  background-color: #e8f0fe;
  border-top-left-radius: 0;
}

.user .message-bubble {
  background-color: #cbffe9;
  border-top-right-radius: 0;
}
.message-bubble p {
  margin: 0;
  line-height: 1.4;
  word-wrap: break-word;
  white-space: pre-wrap;
}
.message-time {
  font-size: 11px;
  opacity: 0.7;
  margin-top: 4px;
  display: block;
  text-align: right;
  color: #666;
}

.typing-indicator {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px 0;
}
.typing-indicator span {
  height: 8px;
  width: 8px;
  margin: 0 2px;
  background-color: #888;
  border-radius: 50%;
  display: inline-block;
  opacity: 0.6;
  animation: typing 1s infinite ease-in-out;
}
.typing-indicator span:nth-child(1) {
  animation-delay: 0s;
}
.typing-indicator span:nth-child(2) {
  animation-delay: 0.2s;
}
.typing-indicator span:nth-child(3) {
  animation-delay: 0.4s;
}
@keyframes typing {
  0% {
    transform: translateY(0);
    opacity: 0.6;
  }
  50% {
    transform: translateY(-5px);
    opacity: 1;
  }
  to {
    transform: translateY(0);
    opacity: 0.6;
  }
}
.chatbot-input {
  display: flex;
  align-items: center;
  padding: 10px 10px;
  border-top: 1px solid #e9ecef;
  background-color: #fff;
}
.chatbot-input input {
  flex: 1;
  border: none;
  outline: none;
  padding: 12px 16px;
  border-radius: 12px;
  background-color: #f1f3f4;
  font-size: 14px;
  color: #333;
}
.chatbot-input input::placeholder {
  color: #888;
}
.send-btn {
  background-color: #03346e;
  color: #fff;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  margin-left: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  font-size: 16px;
}
.send-btn:hover {
  background-color: #0253a4;
  transform: scale(1.05);
}
.action-bubble {
  margin-left: 48px;
  margin-top: 4px;
  padding: 8px 8px;
  border-radius: 18px;
  box-shadow: 0 2px 6px #00000014;
  position: relative;
  min-width: 40px;
  display: inline-block;
  background-color: #e8f0fe;
  color: #222;
  border: 1px solid #d0d7e0;  
}

.action-bubble button {
  background-color: #d8d8d9;
  border: none;
  margin: 2px;
  padding: 8px 14px;
  border-radius: 16px;
  font-size: 13px;
  cursor: pointer;
  transition: all 0.2s ease;
  color: #03346e;
  font-weight: 500;
}
.action-bubble button:hover {
  background-color: #e1e4e8;
  transform: translateY(-2px);
}

.action-bubble img {
  max-width: 100%;
}

</style>
