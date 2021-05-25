<template>
  <div class="container">
    <div class="item">
      <VueRecordAudio @result="onResultAudio" mode="press" />
      <span>Audios</span>
      <audio
        v-for="(audio, index) in audios"
        :src="audio"
        :key="index"
        controls="controls"
      />
    </div>
    <div class="item">
      <VueRecordVideo @result="onResultVideo" mode="press" />
      <span>Videos</span>
      <video
        v-for="(video, index) in videos"
        :src="video"
        :key="index"
        controls="controls"
      />
    </div>
    <h2>Record RTC</h2>
    <button class="btn" @click="start" :disabled="playing">Play</button>
    <button class="btn" @click="stop" :disabled="!playing">Stop</button>
    <video
      width="500px"
      height="300px"
      ref="video"
      controls
      autoplay
      playsinline
    ></video>
  </div>
</template>

<script>
import RecordRtc from "recordrtc";
export default {
  data() {
    return {
      recorder: null,
      playing: false,
      audios: [],
      videos: [],
    };
  },
  methods: {
    async start() {
      const displaymediastreamconstraints = {
        video: true,
      };
      let stream;
      if (navigator.mediaDevices.getDisplayMedia) {
        stream = await navigator.mediaDevices.getDisplayMedia(
          displaymediastreamconstraints
        );
      } else {
        stream = navigator.getDisplayMedia(displaymediastreamconstraints);
      }
      this.recorder = new RecordRtc(stream, {
        type: "video",
      });
      this.recorder.startRecording();
      this.recorder.screen = stream;
      this.playing = true;
    },
    stop() {
      this.recorder.stopRecording(this.onStop);
    },
    onStop() {
      const video = this.$refs.video;
      video.src = video.srcObject = null;
      video.src = URL.createObjectURL(this.recorder.getBlob());
      this.recorder.screen.stop();
      this.recorder.destroy();
      this.recorder = null;
      this.playing = false;
    },
    onResultAudio(blob) {
      console.log("bogogoog", blob);
      this.audios.push(`${window.URL.createObjectURL(blob)}#t=3,5`);
    },
    onResultVideo(blob) {
      this.videos.push(window.URL.createObjectURL(blob));
    },
  },
};
</script>

<style lang="stylus" scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn {
  margin: 10px 0px;
  width: 200px;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.item {
  padding: 20px 20px;
}
</style>