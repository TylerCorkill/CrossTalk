<template>
<div>
  <text-box
    v-for="message in transcript"
    :message="message"
    :key="message.id"
  ></text-box>
</div>
</template>


<script>
import TextBox from './TextBox.vue'
export default {
  data: function () {
    return {
      recognition: null,
      transcript: [],
      callHasEnded: false,
    }
  },
  methods: {
    initializeSpeechRecognition: function () {
      console.log('Starting speech recognition...');
      var SpeechRecognition = SpeechRecognition || webkitSpeechRecognition;

      this.recognition = new SpeechRecognition();
      this.recognition.lang = 'en-US';
      this.recognition.interimResults = false;
      this.recognition.maxAlternatives = 5;
      this.recognition.continuous = true;

      this.recognition.onend = () => {
        if (this.callHasEnded) {
          console.log('Stopped speech recognition.');
        } else {
          this.recognition.start();
        }
      };

      this.recognition.onresult = (event) => {
        var latest = event.results.length - 1;
        var result = event.results[latest][0].transcript
        var timestamp = new Date(Date.now());
        timestamp = timestamp.toLocaleTimeString();
        this.transcript.push({id: timestamp, text: result});
      };
      
      this.recognition.start();
    },
    
    listen: function () {
      if (this.callHasEnded) {
        this.recognition.stop();
      } else {
        this.initializeSpeechRecognition();
      }
    },
    
    toggleListen: function () {
      this.callHasEnded = !this.callHasEnded;
      this.listen();
    },
  },
  mounted: function () {
    this.listen();
  },
  beforeDestroy: function () {
    if (this.recognition) {
      this.callHasEnded = true;
      this.recognition.stop();
    }
  },
  components: {
    TextBox
  }
}

</script>


<style>

</style>