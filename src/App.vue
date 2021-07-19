<template>
  <div id="app">
       <h2 class="center-align">Photos Galery</h2>
       <hr>
     <div class="row">
        <div class="col s10 offset-s1 m4 offset-m4 center">
          <div class="card">
            <div class="card-image">
              <img :src="photo.data.cropped_picture" :alt="photo.id">
            </div>
            <div class="card-content">
              <p>Autor: {{photo.data.author}}</p>
              <p>tags: {{photo.data.tags}}</p>
              <p>camera: {{photo.data.camera}}</p>
            </div>
            <div class="card-action center-align">
              <a @click="prev()"> Prev</a>
              <a @click="openFull()"> Open Full</a>
              <a @click="next()"> Next</a>
            </div>
          </div>
        </div>
      </div>

      <div id="fullImg" class="row">
      </div>

  </div>
</template>

<script>
import axios from "axios";
import M from 'materialize-css'

export default {

  data(){
    return{
      post: {
        method: 'post',
        url: 'http://interview.agileengine.com/auth',
        headers: { 
          'Content-Type': 'text/plain'
        },
        data : { apiKey: "23567b218376f79d9415" }
        },
        photos: [],
        photo: {
          data: {}
        },
        n: 0,
        lenght: 0,
        token: null,
        page: 0
    }
  },
  created(){
    this.auth()
  },
  beforeMount(){
    M.AutoInit()
  },
  methods:{
    
    auth(){
      let self = this
      axios(this.post).then(function (response) {
        self.token =  response.data.token
        self.getPhotos(response.data.token)

      }).catch(function (error) {
        console.log(error);
      });
    },

    getPhotos(token){
      let self = this
      axios({
        method: 'get',
        url: 'http://interview.agileengine.com/images?page='+self.page,
        headers: { 
          'Authorization': 'Bearer ' +token
        }
      }).then(function (response) {
        self.photos = response.data.pictures
        console.log(self.photos)
        response.data.pictures.forEach(() => {
          self.lenght++
        });
        self.getMoreDetails(response.data.pictures[0].id)
        console.log(response.data.pictures[0].id)

      }).catch(function (error) {
        console.log(error)
      });
    },
    prev(){
      if(this.page >= 1 && this.n == 0){
        --this.page
        this.lenght = 0
        this.getPhotos(this.token)
      }
      this.photo = this.photos[this.n = this.n == 0 ? this.lenght - 1 : this.n - 1]
      this.getMoreDetails(this.photo.id)
    },
    next(){
      if(this.n == this.lenght -1){
        ++this.page
        this.lenght = 0
        this.n = 0
        this.getPhotos(this.token)
      }
      this.photo = this.photos[this.n = this.n == this.lenght -1  ? 0 : this.n + 1]
      this.getMoreDetails(this.photo.id)
    },
    getMoreDetails(id){
      let self = this
        axios({
        method: 'get',
        url: 'http://interview.agileengine.com/images/' + id,
        headers: { 
          'Authorization': 'Bearer ' +this.token
        }
      }).then(function (response) {
        self.photo = response
        console.log(response)

      }).catch(function (error) {
        console.log(error)
      });
    },
    openFull(){
      document.getElementById("fullImg").classList.add("fullModal")
      document.body.classList.add("opacity")
      let img = document.createElement("img")
      img.id = "imgFull"
      img.classList.add("bigImg", "s12")
      let btn = document.createElement("button")
      btn.id = "btnFull"
      btn.innerHTML = "Close"
      btn.onclick = this.closeFull
      btn.classList.add("center", "s12", "btn", "red")
      img.src = this.photo.data.full_picture
      document.getElementById('fullImg').appendChild(img)
      document.getElementById('fullImg').appendChild(btn)
    },
    closeFull(){
      
      let parent = document.getElementById("fullImg");
      parent.removeChild(document.getElementById("btnFull"));
      parent.removeChild(document.getElementById("imgFull"));

      document.getElementById("fullImg").classList.remove("fullModal");
      document.body.classList.remove("opacity");
    }
  }
}
</script>

<style>
.main{
  max-width: 50vh;
  display: flex;
  justify-content: center;
}
.fullModal{
  position: absolute; /* Stay in place */
  z-index: 100; /* Sit on top */
  top: 10%;
  display: flex;
  justify-content: center;
  text-align: center;
  max-width: 100%;
  max-height: 100%;
  width: 100% ;
  height: 100% ;

}
.opacity{
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}
.bigImg{
  max-width: 100%;
  max-height: 100%;
  width: 80%;
  height: 80%;
}
</style>
