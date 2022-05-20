# safari seek video as data have been loaded

## Project setup

### Pre-requisites

- nodejs
- yarn
- @vue/cli

### Install dependencies

```
yarn install
```

### AccuratePlayer key

Edit `.env.demo` and replace `put_key_here` with AccuratePlayer license key

### Run app

```
yarn serve:demo
```

## The background

We are using the player and it's pointplugin feature and we want to seek as soon as "in" marker is there.

Also I tried pointplugin seekToIn function which in background calls the same function as the one I used in this example

### Expected behavior

As soon as the video is loaded and player seek method is called, player should seek to that frame and timeline time indicator too.

Playing the player (play icon) should start video from desired frame and time indicator should move forward as video plays.

### Actual behavior

While on Chrome everything works fine, the Safari web browser has a problem to seek the video but timeline time indicator jumps to desired frame.

Video is loading from the beginning (not desired) and timeline time indicator freezes (not desired).

I tried to use different hls events but no luck with safari. In this example I am using html video element "onvideoloaded" event.

I also tried hls MANIFEST_LOADED as well as FRAGMENT_LOADED events but still no luck.

**NOTES**

Reload page between experiments.
