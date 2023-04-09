<template>
  <div id="app">
    <div v-if="messages.length > 0" class="contentBlock">
      <div v-for="message in messages" :key="message.id">
        <strong>{{ message.user }}:</strong> 
        <label class="question" :class="{answer : message.user === 'Bot'}"> 
          {{ message.text }}
        </label>
      </div>  
    </div>

    <div class="loading" v-show="isLoading">								
      Waiting<span class="textDot">.</span>
			<span class="textDot">.</span>
			<span class="textDot">.</span>
    </div>

    <div class="inputBlock">
      <input v-model="inputText" />
      <button :disabled="isLoading" @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
import { Configuration, OpenAIApi } from "openai"

const OPEN_AI_KEY = "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"
const config = new Configuration({
  apiKey: OPEN_AI_KEY
})

const openai = new OpenAIApi(config);

export default {
  name: 'App',
  data() {
    return {
      messages:[],
      inputText:"",
      isLoading: false
    }
  },
  methods: {
    async sendMessage (){
      this.isLoading = true
      let question = this.inputText
      const prompt = {
        id: this.messages.length + 1,
        user: 'You',
        text: this.inputText
      };
      this.messages.push(prompt);
      this.inputText = "";
      let response = await openai.createChatCompletion({
          model: "gpt-3.5-turbo",
          messages: [{role: "user", content: question}],
      })
      const message = {
        id: this.messages.length + 1,
        user: 'Bot',
        text: response.data.choices[0].message.content
      };
      this.messages.push(message);
      this.isLoading = false
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 100%;
  padding: 0px;
  margin: 0px;
}

button{
  width: 100px;
  height: 32px;
}

input{
  width: calc(100% - 90px);
  height: 32px;
  padding: 0 15px;
}

.contentBlock{
  margin-bottom: 20px;
}

.loading{
  margin: 10px 0;
  text-align: center;
  font-size: 15px;
}

.inputBlock{
  width: 100%;
  display: flex;
  align-items: center;
  column-gap: 10px;
}

.question{
  color: coral;
  
}

.answer{
  color: #2c3e50;
  white-space: pre-line;
}

.textDot:nth-last-of-type(1){
  animation: wip1 3s 0s infinite;
}
.textDot:nth-last-of-type(2){
	animation: wip1 3s 0.2s infinite;
}
.textDot:nth-last-of-type(3){
	animation: wip1 3s 0.4s infinite;
}

@keyframes wip1 {
	0% {
		opacity: 0;
	}
	10%,
	50% {
		opacity: 1;
	}
	60%,
	100% {
		opacity: 0;
	}
}
</style>
