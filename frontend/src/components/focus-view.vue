<template>
  <div id="focus-view" class="fadeIn">
    

    <div class="fcs-container-menu-top">
    <router-link class="fcs-task-btn" :to="{path:'/file/'+file._id}"><icon name="arrow-left" scale="1"></icon></router-link>
   <!--  <div id="ctr"> -->
      <!-- <div class="fcs-ctr"> -->
      <!-- <icon name="arrows-v" class="ico-fa" scale="1"></icon> -->
      <div class="fcs-ctr">
      <icon name="arrows-v"  class="ico-fa" scale="1"></icon>
      <div href="#"  id="ctr" style="color:white; align-self: right"> 0</div>
      </div>
      <!-- </div>
      <div class="fcs-ctr">
      <icon name="arrows-h" class="ico-fa" scale="1"></icon> -->'
      <div class="fcs-ctr">
      <icon name="arrows-h"  class="ico-fa" scale="1"></icon>
      <div href="#"  id="ctr1" style="color:white; align-self: right"> 0</div>
      </div>'
      
     <!--  </div> -->
   <!--  </dir> -->
     
      </div>
      <div id="fcs-title">
      <h1>{{file.name}}</h1>
      </div>
      <div id="fcs-code">
      <textarea name="editor" id="editor">{{file.source}}</textarea>
      </div>
      <div id="fcs-container-menu">
      <a href="#" v-on:click="update" class="btn" id="save-btn"><icon name="floppy-o" scale="2"></icon></a>
      <a href="#" v-on:click="remove" class="btn" id="delete-btn"><icon name="trash" scale="2"></icon></a>
      </div>
  </div>
</template>

<script>
// enable ajax
let Vue = require('vue');
let VueResource = require('vue-resource');
Vue.use(VueResource);

import CodeMirror from 'codemirror'
import 'codemirror/mode/clike/clike';
import Icon from 'vue-awesome/components/Icon.vue';
import 'vue-awesome/icons';

export default {
  name: 'focus-view',
  components: {
    "icon":Icon
  },
  mounted: function () {
    this.$el.querySelector('#editor')
    this.cm = CodeMirror.fromTextArea(this.$el.querySelector('#editor'), {
      lineNumbers: true,
      mode: 'text/x-java'
    });
    this.cm.setSize("80vw","70vh")

    this.container =  this.$el.querySelector('#single-wrapper');
    // this.container.style.width=this.width+"vmin";
    // this.container.style.height=this.height+"vmin";
    this.fileid = this.$route.params.id;
    this.show();
  },

  data: function () {
    return {
      file: {name:"Getting filname", source:"Loading source..."},
      'width':  80 ,
      'height':  80,
      cm:{},
      fileid:{} 
    };
  },

  methods: {
            show:function(){
         var self = this;
            this.$http({url: 'http://localhost:3000/api/files/'+ self.fileid, method: 'GET'}).then(function (response) {
              console.log(response.body);
             
              self.file = response.body;
              self.cm.setValue(self.file.source);
              self.count();

  }, function (response) {
      // error callback   
      self.cm.setValue("File not found");

 }); 
      },
      count:function(){
         var self = this;
            this.$http({url: 'http://localhost:3000/api/files/'+ self.fileid +'?rel=count', method: 'GET'}).then(function (response) {
              console.log(response.body);
              
              if(response.body){
             
              let ctr = this.$el.querySelector('#ctr');
              if(response.body != 0){
              
              ctr.innerHTML = response.body-1;
              }
              else{
                 ctr.innerHTML = 0;
              }
              let ctr1 = this.$el.querySelector('#ctr1');
               if(self.file.relations.length != 0){
              ctr1.innerHTML = (self.file.relations.length-1).toString();
              }
              else{
                 ctr1.innerHTML = 0;
              }
              }

             
  }, function (response) {
      // error callback   
  }); 
      },
      update: function(event){
      var self = this;
      this.file.source = this.cm.getValue();
      this.$http({url: 'http://localhost:3000/api/files/'+ this.fileid, body:this.file, method: 'PUT'}).then(function (response) {

      // success callback
      self.file = response.body;
      this.cm.setValue(response.body.source);

      }, function (response) {
      // error callback
  });
    },
        remove: function(event){
        var self = this;
        this.$http({url: 'http://localhost:3000/api/files/'+ this.fileid, method: 'DELETE'}).then(function (response) {

      // success callback
      if(response.body){
    
      self.file = {};
      self.$router.replace('/');
      }

  }, function (response) {
      // error callback
  });
    }
}
}

</script>
<style src="codemirror/lib/codemirror.css">
</style>
<style src="codemirror/theme/monokai.css">
</style>
<style>
  #fcs-container-menu{
    /*background: rgba(255, 0, 0, 1);*/
    position: absolute;
    bottom: 0px;
    right: 10%;
    width: 80%;
    height: 10%;
    background: grey;
    display: inline-flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-content: stretch;
  }
  .fcs-container-menu-top{
    /*background: rgba(255, 0, 0, 1);*/
    position: absolute;
    top: 0px;
    right: 10%;
    left: 10%;
    width: 80%;
    height: 5%;
    background: #262626;
    display: inline-flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-content: stretch;
  }
  .fcs-task-btn {
    /*background-color: #666699;*/
    background-color: #990000;
    cursor: pointer;
    color: #ffffff;
    font-family: Helvetica, Arial;
    font-size: 2vmin;
    padding: 1.5vmin 1.5vmin;
    text-decoration: none;
    /*position: absolute;*/
    width: 10%;
    max-width: 10%;
    text-align: center;
    top: 0px;
    left: 0px;
    z-index: 1000000;
  }
  .fcs-task-btn:hover{
    background-color: #660000!important;
  }
  .ico-fa{
    color:#990000;
    text-align: left;
  }
  
  .btn:hover {
    background-color: #4d4d4d!important;
  }
  .exit-btn {
    background-color: #991f00;
    cursor: pointer;
    color: #ffffff;
    font-family: Helvetica, Arial;
    font-size: 2vmin;
    padding: 1.5vmin 1.5vmin;
    text-align: center;
    text-decoration: none;
    width: 10%;
  }
  .exit-btn:hover{
    background-color: #e62e00!important;
  }
  
  .btn {
    background-color: #262626;
    /*display: inline-block;*/
    flex-grow: 1;
    cursor: pointer;
    color: white;
    font-family: 'Helvetica Neue Thin',Helvetica, Arial;
    font-size: 140%;
    padding: 2%;
    width: auto;
    text-decoration: none;
    text-align: center;

  }
  .fcs-ctr {
    background-color: inherit;

    /*display: inline-block;*/
    display: flex;
    align-items: center;
    cursor: default;
    color: white;
    font-family: 'Helvetica Neue Thin',Helvetica, Arial;
    font-size: 2vmin;
    padding: 1.5vmin 1.5vmin;
    width: auto;
    text-decoration: none;
    text-align: left;
    border-left: 1px;
    border-left-color: #ffffff;
    border-left-style: solid;
    max-width: 5% !important;

  }
  .fcs-ctr:nth-child(2){
    margin-left: auto; 
  }
  #fcs-title{
    position: absolute;
    top: 5%;
    width: 100%;
  }
  #focus-view {
    background-color: #333333;
    width: 100vw;
    height: 100vh;
    position: absolute;
    left: 0;
    top:0;
  }
  #delete-btn{
  background: #990000;
  }
  #delete-btn:hover{
  background: red !important;
  }

  .CodeMirror pre {
    white-space: pre-wrap;
    word-break: break-all;
    word-wrap: break-word;
  }
  .CodeMirror{
    height: 70%;
    width: 100%; 
    font-size: 16pt;
  }
  #fcs-code{
    padding-left: 10%;
    padding-right: 10%;
    position: absolute;
    top: 15%;
    left: 0;
  }
  
  h1 {
    color: white;
    font-family: 'Helvetica Neue', Helvetica, Arial;
    font-weight: normal;
    text-align: center;
  }

.loader {
    border: 16px solid #f3f3f3; /* Light grey */
    border-top: 16px solid #3498db; /* Blue */
    border-radius: 50%;
    width: 120px;
    height: 120px;
    animation: spin 2s linear infinite;
    margin: 0 auto;
    
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}


.fadeIn{
animation-name: fadeInKey; /* the name of the animation we defined above */
animation-iteration-count: 1; /* how many times the animation will play */
animation-timing-function: ease-in; /* how the animation will behave */
animation-duration: 0.6s; /* the duration of the animation */
}

@keyframes fadeInKey {
    0% {
        opacity: 0;
    }
    100% {
        opacity: 100;
    }
}

 </style>

<style src="../../node_modules/highlight.js/styles/atom-one-dark.css">
 </style>