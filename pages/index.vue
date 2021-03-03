<template>
  <div class="container">
    <header class="header">
      <Logo class="logo" />
      <a v-if="!token" :href="loginLink" class="btn">Login to Spotify</a>
    </header>

    <Player
      v-if="token && !noData"
      :item="item"
      :is-playing="isPlaying"
      :progress-ms="progressMs"
    />

    <p v-if="noData">
      You need to be playing a song on Spotify, for something to appear here.
    </p>
  </div>
</template>

<script>
import { authEndpoint, clientId, redirectUri, scopes } from '../config.js'
import hash from '../hash.js'

export default {
  data() {
    return {
      token: null,
      item: {
        album: {
          images: [{ url: '' }],
        },
        name: '',
        artists: [{ name: '' }],
        duration_ms: 0,
      },
      isPlaying: 'Paused',
      progressMs: 0,
      noData: false,
      interval: null,
    }
  },
  computed: {
    loginLink() {
      const joinedScopes = scopes.join('%20')
      return `${authEndpoint}?client_id=${clientId}&redirect_uri=${redirectUri}&scope=${joinedScopes}&response_type=token&show_dialog=true`
    },
  },
  mounted() {
    const _token = hash.access_token

    if (_token) {
      this.token = _token
      this.getCurrentlyPlaying(_token)
    }

    this.interval = setInterval(() => this.tick(), 5000)
  },
  destroyed() {
    clearInterval(this.interval)
  },
  methods: {
    tick() {
      if (this.token) {
        this.getCurrentlyPlaying(this.token)
      }
    },
    async getCurrentlyPlaying(token) {
      const data = await this.$axios.$get(
        'https://api.spotify.com/v1/me/player',
        {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        }
      )

      if (!data) {
        this.noData = true
        return
      }

      this.item = data.item
      this.isPlaying = data.is_playing
      this.progressMs = data.progress_ms
      this.noData = false
    },
  },
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html,
body {
  height: 100%;
}

body {
  background-color: #333;
  color: #eee;
  font-family: Helvetica, Arial;
  font-size: 3vmin;
}

.container {
  text-align: center;
}

.header {
  align-items: center;
  color: #ffffff;
  display: flex;
  font-size: calc(10px + 2vmin);
  flex-direction: column;
  justify-content: center;
  min-height: 100vh;
}

.btn {
  background-color: transparent;
  border: 0.2em solid #1ecd97;
  border-radius: 2em;
  color: #1ecd97;
  cursor: pointer;
  font-size: 3vmin;
  padding: 0.7em 1.5em;
  text-transform: uppercase;
  transition: all 0.25s ease;
}

.btn:hover {
  background: #1ecd97;
  color: #333;
}
</style>
