<template>
    <!--<div id="wrapper">-->
    <!--<img id="logo" src="~@/assets/logo.png" alt="electron-vue">-->
    <!--<main>-->
    <!--<div class="left-side">-->
    <!--<span class="title">-->
    <!--Welcome to your new project!-->
    <!--</span>-->
    <!--<system-information></system-information>-->
    <!--</div>-->

    <!--<div class="right-side">-->
    <!--<div class="doc">-->
    <!--<div class="title">Getting Started</div>-->
    <!--<p>-->
    <!--electron-vue comes packed with detailed documentation that covers everything from-->
    <!--internal configurations, using the project structure, building your application,-->
    <!--and so much more.-->
    <!--</p>-->
    <!--<button @click="open('https://simulatedgreg.gitbooks.io/electron-vue/content/')">Read the Docs</button><br><br>-->
    <!--</div>-->
    <!--<div class="doc">-->
    <!--<div class="title alt">Other Documentation</div>-->
    <!--<button class="alt" @click="open('https://electron.atom.io/docs/')">Electron</button>-->
    <!--<button class="alt" @click="open('https://vuejs.org/v2/guide/')">Vue.js</button>-->
    <!--</div>-->
    <!--</div>-->
    <!--</main>-->
    <!--</div>-->
    <mu-container>
        <mu-form :model="form" label-width="100" v-on:submit.prevent>
            <mu-form-item prop="input">
                <mu-text-field v-model="form.search" v-on:keyup.enter="submit"></mu-text-field>
            </mu-form-item>
        </mu-form>
        <p>Pesquisado: {{ form.search }} - Baixado {{ percentage }} %</p>
        <audio controls ref="audio"></audio>

        <mu-row gutter>
            <mu-col v-for="video in videos">
                <mu-card>
                    <mu-card-media :title="video.title">
                        <img :src="video.thumbnails.high.url" :alt="video.title">
                    </mu-card-media>
                    <mu-card-title :title="video.title"></mu-card-title>
                    <mu-card-text>{{ video.description }}</mu-card-text>
                    <mu-card-actions>
                        <mu-button v-on:click="play(video)">Play</mu-button>
                    </mu-card-actions>
                </mu-card>
            </mu-col>
        </mu-row>
    </mu-container>
</template>

<script>
  // import SystemInformation from './LandingPage/SystemInformation';
  import * as youtubeSearch from 'youtube-search';
  // import fs from 'fs';
  // import path from 'path';
  import MediaElementWrapper from 'mediasource';
  import * as ytdl from 'ytdl-core';

  export default {
    name: 'youtube-as-player',
    // components: { SystemInformation },
    data: () => ({
      form: {},
      optsYoutube: {},
      videos: {},
      percentage: {},
    }),
    methods: {
      submit() {
        youtubeSearch(this.form.search, youtubeSearch.YouTubeSearchOptions = {
          maxResults: 10,
          key: 'AIzaSyBI4jdoB25l-ankFFhqRau801EUSh5Plgs',
          type: 'video',
        }, (err, results) => {
          if (err) {
            return console.error(err);
          }

          this.videos = results;
          return results;
        });
      },
      play(video) {
        // ytdl(video.link, { quality: 'highestaudio' })
        this.$refs.audio.controls = true;
        this.$refs.audio.autoplay = true;
        // this.$refs.audio.play();
        const wrapper = MediaElementWrapper(this.$refs.audio);
        const writable = wrapper.createWriteStream('audio/mpeg;');
        this.$refs.audio.addEventListener('error', () => {
          // listen for errors on the video/audio element directly
          const errorCode = this.$refs.audio.error;
          const [detailedError] = wrapper.detailedError;
          console.error(errorCode);
          console.error(detailedError);
          // wrapper.detailedError will often have a more detailed error message
        });
        writable.on('error', (err) => {
          console.log(err);
        });

        ytdl(video.link, {
          quality: 'highestaudio',
          // filter: 'audioonly',
        })
        // .pipe(fs.createWriteStream(path.join(__static, '/blla.mp3')))
          .pipe(writable)
          .on('progress', (chunkLength, downloaded, total) => {
            console.log(downloaded / total);
            this.percentage = downloaded / total;
          })
          .on('finish', (audio) => {
            console.log('finish', audio);
          });
      },
    },
  };
</script>

<style>
    /*@import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');*/
    @import url('https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,400italic');
    @import url('https://cdn.bootcss.com/material-design-icons/3.0.1/iconfont/material-icons.css');

    /** {*/
    /*box-sizing: border-box;*/
    /*margin: 0;*/
    /*padding: 0;*/
    /*}*/

    /*body { font-family: 'Source Sans Pro', sans-serif; }*/

    /*#wrapper {*/
    /*background:*/
    /*radial-gradient(*/
    /*ellipse at top left,*/
    /*rgba(255, 255, 255, 1) 40%,*/
    /*rgba(229, 229, 229, .9) 100%*/
    /*);*/
    /*height: 100vh;*/
    /*padding: 60px 80px;*/
    /*width: 100vw;*/
    /*}*/

    /*#logo {*/
    /*height: auto;*/
    /*margin-bottom: 20px;*/
    /*width: 420px;*/
    /*}*/

    /*main {*/
    /*display: flex;*/
    /*justify-content: space-between;*/
    /*}*/

    /*main > div { flex-basis: 50%; }*/

    /*.left-side {*/
    /*display: flex;*/
    /*flex-direction: column;*/
    /*}*/

    /*.welcome {*/
    /*color: #555;*/
    /*font-size: 23px;*/
    /*margin-bottom: 10px;*/
    /*}*/

    /*.title {*/
    /*color: #2c3e50;*/
    /*font-size: 20px;*/
    /*font-weight: bold;*/
    /*margin-bottom: 6px;*/
    /*}*/

    /*.title.alt {*/
    /*font-size: 18px;*/
    /*margin-bottom: 10px;*/
    /*}*/

    /*.doc p {*/
    /*color: black;*/
    /*margin-bottom: 10px;*/
    /*}*/

    /*.doc button {*/
    /*font-size: .8em;*/
    /*cursor: pointer;*/
    /*outline: none;*/
    /*padding: 0.75em 2em;*/
    /*border-radius: 2em;*/
    /*display: inline-block;*/
    /*color: #fff;*/
    /*background-color: #4fc08d;*/
    /*transition: all 0.15s ease;*/
    /*box-sizing: border-box;*/
    /*border: 1px solid #4fc08d;*/
    /*}*/

    /*.doc button.alt {*/
    /*color: #42b983;*/
    /*background-color: transparent;*/
    /*}*/
</style>
