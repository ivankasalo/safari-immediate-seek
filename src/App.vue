<template>
  <div id="app">

    <div class="wrapper">
      <div class="player-16-9">
        <video ref="video" class="player"></video>
        <apc-controls ref="controls"></apc-controls>
      </div>
      <ap-timeline-basic />

      <ak-style-injector />
    </div>

  </div>
</template>

<script>
import '@accurate-player/accurate-player-controls'
import { HlsPlayer } from '@accurate-player/accurate-player-hls'
import { HotkeyPlugin } from '@accurate-player/accurate-player-plugins'
import { ApTimelineBasic } from '@accurate-player/accurate-player-components-external'
import '@accurate-player/accurate-player-components-external/dist/styles/theme-light.css'
import Hls from 'hls.js'
import AkStyleInjector from '@/components/AkStyleInjector'

ApTimelineBasic.register()

export default {
  name: 'App',
  components: {
    AkStyleInjector
  },
  data: () => ({
    player: HlsPlayer,
    video: HTMLVideoElement,
    controls: HTMLElement,
    hotkey: undefined,
    src:
      'https://bitdash-a.akamaihd.net/content/sintel/hls/playlist.m3u8',
    frameRate: {
      denominator: 1,
      numerator: 25
    },
    seekFlag: true
  }),
  methods: {
    init () {
      this.player = new HlsPlayer(
        this.video,
        process.env.VUE_APP_ACCURATE_PLAYER_KEY
      )

      this.loadMedia()
      this.initPlugins()
      this.initControls()
      this.player.api.hlsPlayer.on(Hls.Events.ERROR, (event, data) => {
        console.log('error', event, data)
      })

      this.player.api.hlsPlayer.on(Hls.Events.MANIFEST_LOADED, () => {
        // this seeks and works as well as with FRAGMENT_LOADED event
        // if (this.seekFlag) {
        //   this.player.api.seek({
        //     time: this.frameRate.numerator * 60,
        //     mode: 'Absolute',
        //     format: 1
        //   })
        // }
        // this.seekFlag = false
      })

      document.getElementsByTagName('ap-timeline-basic')[0].player = this.player
    },
    loadMedia () {
      this.player.api.loadVideoFile({
        src: this.src,
        enabled: true,
        frameRate: this.frameRate,
        dropFrame: false
      })
    },
    initPlugins: function () {
      this.hotkey = new HotkeyPlugin(this.player)
    },
    initControls: function () {
      // Check if method exist, otherwise wait 100ms and try again.
      if (!this.controls.init) {
        setTimeout(() => {
          this.initControls()
        }, 100)
        return
      }
      // The Polymer 3.0 project is currently written in Javascript. Will be converted to TypeScript with typings shortly!
      this.controls.init(this.video, this.player, {
        saveLocalSettings: true // Enables storing of simple settings as volume, timeFormat and autoHide.
      })
    }
  },
  mounted () {
    this.video = this.$refs.video
    this.video.onloadeddata = () => {
      // this seeks to 1 min into the video
      this.player.api.seek({
        time: this.frameRate.numerator * 60,
        mode: 'Absolute',
        format: 1
      })
    }
    this.controls = this.$refs.controls

    this.init()
  }
}
</script>

<style>
.wrapper {
  max-width: 50%;
  margin: 200px auto;
}

.player-16-9 {
  position: relative;
  padding: 0;
  padding-top: 56.25%;
}

.player {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: black;
}
</style>
