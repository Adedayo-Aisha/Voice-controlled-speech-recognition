<template>
  <div :style="{backgroundColor: themes[theme].backgroundColor,color:themes[theme].textColor}" class="container">
    
    <h1 class="title">Voice-Controlled Speech Recognition</h1>
    <button class="button" @click="startRecording" :disabled="isRecording">Start</button>
    <button class="button" @click="stopRecording" :disabled="!isRecording">Stop</button>
    <button class="button" @click="clearText">Clear</button>
    <button class="button" @click="saveText">Save</button>
    <div ref="textArea" class="text-area">
      <p v-for="(line, index) in textLines" :key="index">{{ line }}</p>
    </div>
    <div>
      <label for="select">Theme</label>
    <select id="select" @change="changeTheme($event.target.value)">
      <option value="default">Default</option>
      <option value="dark">Dark</option>
    </select>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue';
// import SpeechRecognition from 'speech-recognition'

const textArea = ref(null);
const text = ref('');
const textLines = ref([]);
const isRecording = ref(false);

const SpeechRecognition =
  window.SpeechRecognition || window.webkitSpeechRecognition;
const recognition = new SpeechRecognition();

recognition.continuous = true;
recognition.interimResults = true;
recognition.lang = navigator.language;

recognition.onresult = (event) => {
  const current = text.value;
  let interimTranscript = '';

  for (let i = event.resultIndex; i < event.results.length; i++) {
    const result = event.results[i];

    if (result.isFinal) {
      interimTranscript += ' ' + result[0].transcript;
    } else {
      interimTranscript += result[0].transcript;
    }
    const transcript = event.results[event.results.length - 1][0].transcript.toLowerCase();
  console.log('Transcript:', transcript);
  
  // Check if recognized command matches any defined pattern
  Object.keys(commands).forEach(command => {
    if (transcript.includes(command)) {
      commands[command]();
    }
  });
  }

  text.value = interimTranscript;
  updateTextLines();
};

//start command
recognition.addEventListener('result',(event) => {
  const current = text.value
  let interimTranscript = '';
  for (let i = event.result.Index; i < event.result.length; i++) {
    const result = event.result[i];
    if (result.isFinal) {
      interimTranscript += ''+result[0].transcript;
    }else{
      interimTranscript += result[0].transcript;
    }
  }
  text.value = interimTranscript;
  if (text.value.toLowerCase.includes('start recording')) {
    startRecording();
  }
});

//stop recording
recognition.addEventListener('result',(event) => {
  const current = text.value
  let interimTranscript = '';
  for (let i = event.result.Index; i < event.result.length; i++) {
    const result = event.result[i];
    if (result.isFinal) {
      interimTranscript += ''+result[0].transcript;
    }else{
      interimTranscript += result[0].transcript;
    }
  }
  text.value = interimTranscript;
  if (text.value.toLowerCase.includes('stop recording')) {
    stopRecording();
  }
});

//clearText
recognition.addEventListener('result',(event) => {
  const current = text.value
  let interimTranscript = '';
  for (let i = event.result.Index; i < event.result.length; i++) {
    const result = event.result[i];
    if (result.isFinal) {
      interimTranscript += ''+result[0].transcript;
    }else{
      interimTranscript += result[0].transcript;
    }
  }
  text.value = interimTranscript;
  if (text.value.toLowerCase.includes('clear')) {
    clearText();
  }
});

//save
recognition.addEventListener('result',(event) => {
  const current = text.value
  let interimTranscript = '';
  for (let i = event.result.Index; i < event.result.length; i++) {
    const result = event.result[i];
    if (result.isFinal) {
      interimTranscript += ''+result[0].transcript;
    }else{
      interimTranscript += result[0].transcript;
    }
  }
  text.value = interimTranscript;
  if (text.value.toLowerCase.includes('save')) {
    saveText();
  }
});
const startRecording = () => {
  recognition.start();
  isRecording.value = true;
};

const stopRecording = () => {
  recognition.stop();
  isRecording.value = false;
};

const clearText = () => {
  text.value = '';
  updateTextLines();
};

const saveText = () => {
  // Add code for saving text
  const blob = new Blob([text.value], { type: 'text/plain' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');

  link.href = url;
  link.download = 'saved-text.txt';
  link.click();

  URL.revokeObjectURL(url);
};

const updateTextLines = () => {
  textLines.value = text.value.trim().split('\n');
};
const theme = ref('default');
const themes = {
  default:{
    backgroundColor:'#ffffff',
    textColor:'#000000'
  },
  dark:{
    backgroundColor:'#000000',
    textColor:'#ffffff'
  },
}
function changeTheme(newTheme){
  theme.value = newTheme
}

const commands = {
  'start recording': startRecording,
  'stop recording': stopRecording,
  'clear': clearText,
  'save':saveText
};



onMounted(() => {
  updateTextLines();
});

</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  text-align: center;
  font-family: Arial, sans-serif;
  /* display: flex; */
  /* flex-direction: column; */
}

.title {
  font-size: 2rem;
  margin-top: 50px;
  margin-bottom: 30px;
}

.button-group {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
  flex-direction: column;
}

.button {
  background-color: #4CAF50;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 1rem;
  margin: 10px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #3e8e41;
}

@media (min-width: 768px){
  .container{
    width: 80%;
  }
  .title {
    font-size: 1.5rem;
  }
  .button-group{
    flex-wrap: wrap;
    
  }
  .button {
    padding: 10px 20px;
    font-size: 0.8rem;
  }
}
/* @media screen and (min-width: 768px) {
     .container{
      width: 80%;
     }
    } */
</style>