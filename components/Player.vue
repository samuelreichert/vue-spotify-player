<template>
  <div class="container">
    <div class="main-wrapper">
      <div class="now-playing__img">
        <img :src="item.album.images[0].url" />
      </div>
      <div class="now-playing__side">
        <div class="now-playing__name">{{ item.name }}</div>
        <div class="now-playing__artist">
          {{ item.artists[0].name }}
        </div>
        <div class="now-playing__status">
          {{ isPlaying ? 'Playing' : 'Paused' }}
        </div>
        <div class="progress">
          <div class="progress__bar" :style="progressBarStyle" />
        </div>
      </div>
      <div class="background" :style="backgroundStyle" />
      {{ ' ' }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    item: {
      type: Object,
      default: () => ({}),
    },
    isPlaying: Boolean,
    progressMs: {
      type: Number,
      default: 0,
    },
  },
  computed: {
    backgroundStyle() {
      return `background-image: url(${this.item.album.images[0].url});`
    },
    progressBarStyle() {
      const progressValue = (this.progressMs * 100) / this.item.duration_ms
      return `width: ${progressValue}%;`
    },
  },
}
</script>

<style>
.background {
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-size: cover;
  background-position: center center;
  filter: blur(8em) opacity(0.6);
  position: absolute;
}

.main-wrapper {
  align-items: center;
  display: flex;
  height: 100%;
  margin: 0 auto;
  justify-content: center;
  position: relative;
  width: 80%;
  z-index: 1;
}

.container {
  align-items: center;
  display: flex;
  justify-content: center;
  height: 100%;
}

.main-container {
  flex: 1;
}

/** Now Playing **/
.now-playing__name {
  font-size: 1.5em;
  margin-bottom: 0.2em;
}

.now-playing__artist {
  margin-bottom: 1em;
}

.now-playing__status {
  margin-bottom: 1em;
}

.now-playing__img {
  float: left;
  margin-right: 10px;
  text-align: right;
  width: 45%;
}

.now-playing__img img {
  max-width: 80vmin;
  width: 100%;
}

.now-playing__side {
  margin-left: 5%;
  width: 45%;
}

/** Progress **/
.progress {
  border: 1px solid #eee;
  height: 10px;
  border-radius: 4px;
  overflow: hidden;
}

.progress__bar {
  background-color: #eee;
  height: 8px;
  transition: width 5s ease-in-out;
}
</style>
