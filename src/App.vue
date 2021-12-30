<template>
  <div id="app">
    <div class="camera">
      <video ref="video" :srcObject.prop="videoSource" id="video" autoplay>
        Video stream not available.
      </video>
      <button @click="takepicture" id="startbutton">Take Photo</button>
    </div>
    <canvas ref="canvas" id="canvas" />
    <div class="output">
      <img
        :src="photoSource"
        id="photo"
        alt="The screen capture will appear in this box."
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      videoSource: null,
      photoSource: null,
    };
  },
  methods: {
    startup() {
      navigator.mediaDevices
        .getUserMedia({ video: true, audio: false })
        .then((stream) => {
          this.videoSource = stream;
        })
        .catch((err) => {
          console.log("An error occurred: " + err);
        });
    },
    takepicture() {
      let context = this.$refs.canvas.getContext("2d");
      context.drawImage(this.$refs.video, 0, 0, 200, 200);
      let data = this.$refs.canvas.toDataURL("image/png");
      this.photoSource = data;
    },
  },
  mounted() {
    this.startup();
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
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  column-gap: 2rem;
}

#canvas {
  display: none;
}

.camera {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  align-items: center;
  grid-column: 1 / 2;
}

#startbutton {
  font-size: 1rem;
  padding: 1rem 2rem;
  border: none;
  outline: none;
  border-radius: 8px;
  cursor: pointer;
}

.output {
  grid-column: 2 / 3;
  background-color: tan;
}
</style>
