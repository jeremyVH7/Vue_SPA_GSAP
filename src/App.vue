<template>
  <div>
    <div id="app">
      <div class='row' id='title-h1-div'><h1 id='title-h1'>{{ title }}</h1></div>
      <NavBar>
        <ul>
          <li><router-link to="/"><img id="vue-logo" src="./assets/logo.png"></router-link></li>
          <li v-for='(link, i) in buttonLinks' :key='i'><NavButton v-bind:routerTo="link[0]" v-bind:buttonText="link[1]"></NavButton></li>
        </ul>
      </NavBar>
      <router-view/>
      <div class='col-sm-1 d-btn btn' id='d-u' v-on:click="move">Do Stuff</div>
      <div class='col-sm-1 d-btn btn' id='pause' v-on:click="pause">Pause</div>
      <div class='col-sm-1 d-btn btn' id='resume' v-on:click="resume">Resume</div>
      <div class='col-sm-1 d-btn btn' id='reverse' v-on:click="reverse">Reverse</div>

    </div>
  </div>
</template>

<script>
import {EventBus} from './components/EventBus.js'
import NavBar from './components/NavBar.vue'
import NavButton from './components/NavButton.vue'
import NavDropdown from './components/NavDropdown.vue'
import NavSearch from './components/NavSearch.vue'
import Home from './components/Home.vue'
import About from './components/About.vue'
import Error404 from './components/Error404.vue'

import TweenMax from "gsap/TweenMax";
import TweenLite from "gsap/TweenLite";
import TimelineLite from "gsap/TimelineLite";
import SplitText from "gsap/SplitText";

export default {
  name: 'app',
  components: {
    NavBar: NavBar,
    NavButton: NavButton,
    Home: Home,
    About: About,
    Error404: Error404
  },
  data () {
    return {
      tl: new TimelineLite(),
      splitTextTimeline: new TimelineLite(),
      title: 'Vue Router Single Page Application - with a bunch of Greensock stuff added in',
      buttonLinks: [
        ['/home', 'Home'],
        ['/about', 'About']

        // ['/error404', 'Error']
      ]
    }
  },
  methods: {
    move: function() {

      TweenLite.set('body', {visibility:"visible"})

      //use position parameter "+=0.5" to schedule next tween 0.5 seconds after previous tweens end
      this.tl.from('.jumbotron', 0.5, {scale:.5, autoAlpha:0}, "+=0.5");

      // 3d rotating
      TweenLite.set('.jumbotron', {css:{transformPerspective:400, transformStyle:"preserve-3d"}});
      this.tl.fromTo('.jumbotron', .05, {css:{autoAlpha:0}}, {css:{autoAlpha:1}, immediateRender:true})
        .to('.jumbotron', 0.7, {css:{rotationY:-30, rotationX:20}});

      this.tl.to('.jumbotron', 1, {css:{rotationY:180, z:-180}, ease:Power2.easeOut}, "+=0.2")
        .to('.jumbotron', 1, {css:{rotationX:360, z:-10}})
        .to('.jumbotron', 1, {css:{rotationY:360, z:0}, ease:Power2.easeOut}, "+=0.2");

      // Nav bar
      this.tl.from('#nav-demo', 1, {left:-200, opacity:0});

      // SplitText stuff
      var h1SplitText = new SplitText('h1', {type:"words"});

      h1SplitText.split({type:"chars, words, lines"})
      this.splitTextTimeline.staggerFrom(h1SplitText.chars, 0.2, {autoAlpha:0, scale:4, force3D:true}, 0.02, 4.0)
        .staggerTo(h1SplitText.words, 0.1, {color:"#8FE402", scale:0.9}, 0.1, "words")
        .staggerTo(h1SplitText.words, 0.2, {color:"white", scale:1}, 0.1, "words+=0.1")
        .staggerTo(h1SplitText.lines, 0.5, {x:0, autoAlpha:1}, 0.2).eventCallback("onComplete", reset)

      var pSplitText = new SplitText('p', {type:"words"});
      pSplitText.split({type:"chars, words"})
      this.tl.staggerFrom(pSplitText.chars, 0.4, {scale:7, autoAlpha:0,  rotationX:-180,  transformOrigin:"100% 50%", ease:Back.easeOut}, 0.02, 4.0);

      /// reset html to original state
      function reset(){
        h1SplitText.revert();
        pSplitText.revert();
      }

    },
    pause: function() {
      this.tl.pause();
      this.splitTextTimeline.pause();
    },
    resume: function() {
      this.tl.resume();
      this.splitTextTimeline.resume();
    },
    reverse: function() {
      this.tl.reverse();
      this.splitTextTimeline.reverse();
    }
  },
  created: function () {
    // EventBus.$on('button-clicked', this.buttonClicked);
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: white;
}

h1, h2 {
  font-weight: normal;
  padding-left: 40px;
  position: relative;
}

body {
  background: grey;
}

ul {
  list-style-type: none;
}

li {
  display: inline-block;
  padding-right: 5px;
}
.d-btn {
  background: blue;
  /* height: 100px;
  width: 100%; */
  cursor: pointer;
}
#nav-demo {
  position: relative;
}
</style>
