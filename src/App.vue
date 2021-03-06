<template>
  <div id="app">
    <div class="media">
      <video
        class="video"
        width="500"
        height="375"
        ref="video"
        :srcObject.prop="videoSource"
        autoplay
      >
        Video stream not available.
      </video>
      <img
        class="image"
        width="500"
        height="375"
        :src="photoSource"
        alt="The screen capture will appear in this box."
      />
    </div>
    <canvas width="500" height="375" ref="canvas" id="canvas" />
    <div class="select-device">
      <label for="videoDevices">
        Camera:
        <select v-model="selectedCamera" name="videoDevices" id="videoDevices">
          <option
            v-for="(camera, index) in videoDevices"
            :key="index"
            :value="camera.deviceId"
          >
            {{ camera.label }}
          </option>
        </select>
      </label>
      <label for="audioDevices">
        Microphone:
        <select v-model="selectedMic" name="audioDevices" id="audioDevices">
          <option
            v-for="(mic, index) in audioDevices"
            :key="index"
            :value="mic.deviceId"
          >
            {{ mic.label }}
          </option>
        </select>
      </label>
    </div>

    <button @click="takepicture" class="button">Take Photo</button>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      videoSource: null,
      photoSource: null,
      videoDevices: null,
      audioDevices: null,
      selectedCamera: null,
      selectedMic: null,
    };
  },
  methods: {
    startup() {
      navigator.mediaDevices
        .getUserMedia({ video: true, audio: true })
        .then((stream) => {
          this.videoSource = stream;
        })
        .catch((err) => {
          console.log("An error occurred: " + err);
        });

      this.clearPhoto();
    },
    takepicture() {
      let context = this.$refs.canvas.getContext("2d");
      context.drawImage(this.$refs.video, 0, 0, 500, 375);
      let data = this.$refs.canvas.toDataURL("image/png");
      this.photoSource = data;
    },
    clearPhoto() {
      const context = this.$refs.canvas.getContext("2d");
      context.fillStyle = "#AAA";
      context.fillRect(0, 0, 500, 375);

      const data = this.$refs.canvas.toDataURL("image/png");
      this.photoSource = data;
    },
    async getConnectedDevices() {
      await navigator.mediaDevices.enumerateDevices().then((devices) => {
        this.videoDevices = devices.filter(
          (device) => device.kind === "videoinput"
        );
        this.audioDevices = devices.filter(
          (device) => device.kind === "audioinput"
        );
      });
      this.selectInitialDevices();
    },
    selectInitialDevices() {
      this.selectedCamera = this.videoDevices[0].deviceId;
      this.selectedMic = this.audioDevices[0].deviceId;
    },
  },
  mounted() {
    this.startup();
    this.getConnectedDevices();
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  min-height: 100vh;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.media {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3rem;
}

#canvas {
  display: none;
}

.video {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  align-items: center;
  border-radius: 8px;
  overflow: hidden;
}

.image {
  border-radius: 8px;
  overflow: hidden;
}

.select-device {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

label {
  font-weight: 800;
}

select {
  height: 2rem;
  font-size: 1rem;
}

.button {
  font-size: 1.3rem;
  font-weight: 700;
  padding: 1.5rem 2.5rem;
  border: none;
  outline: none;
  border-radius: 8px;
  cursor: pointer;
  color: white;
  background-color: royalblue;
}

.button:hover {
  background-color: rgb(36, 68, 167);
}
</style>
