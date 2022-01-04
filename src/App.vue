<template>
<div id="app">
<div class="header">
<div class="apptitle" style="padding-top:10px">
<h2>Gallery</h2>
</div>
<div class="example">
<input v-model="query" @change="getpictures()" type="text" placeholder="search.." name="search"/>
<button class="button1" @click="getpictures()" type="submit">
<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16">
  <path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/>
</svg>
</button>
<button class="button2" @click="completepdf()">download all</button>
</div>
</div>
<div class="resulthub">

<div v-if="defaultView">
  <swiper class="swiper" :options="swiperOption">
  <swiper-slide v-for="(photo,index) in resultpics" :key="index">
  <div class="card piccard">
  <img class="card-img-top" :src="photo.urls.thumb" style="height:250px" alt="Card image cap">
  <ul class="list-group list-group-flush" style="text-align:left">
    <li class="list-group-item">Likes : {{photo.urls.likes}}</li>
    <li class="list-group-item">Created at:{{photo.urls.created_at}}</li>
    <li class="list-group-item">Photographer : {{photo.user.first_name}}</li>
  </ul>
  <div class="card-body">
    <button @click="generatepdf(photo.urls.thumb, photo.id)" class="card-link" style="cursor:arrow">download</button>
  </div>
</div>
  </swiper-slide>
  <div class="swiper-pagination" slot="pagination"></div>
  <div class="swiper-button-prev" slot="button-prev"></div>
  <div class="swiper-button-next" slot="button-next"></div>
  </swiper>
  </div>
<div v-if=!defaultView>
<slidePan :slideDataCollection="resultpics"></slidePan>
</div>
  </div>
  </div>
</template>
 
<script>
 import { Swiper, SwiperSlide } from 'vue-awesome-swiper'
 import slidePan from './components/slider.vue'
 import 'swiper/css/swiper.css'
 import jsPDF from 'jspdf'

  export default {
    name: 'App',
    data() {
      return {
        query:"",
        resultpics:[],
        defaultView:false,
        swiperOption: {
          slidesPerView: 3,
          spaceBetween: 50,
          keyboard: {
            enabled: true,
          },
          pagination: {
            el: '.swiper-pagination',
            clickable: true
          },
          navigation: {
            nextEl: '.swiper-button-next',
            prevEl: '.swiper-button-prev'
          },
          breakpoints: {
            1024: {
              slidesPerView: 3,
              spaceBetween: 40
            },
            768: {
              slidesPerView: 2,
              spaceBetween: 20
            },
            640: {
              slidesPerView: 1,
              spaceBetween: 10
            },
            150: {
              slidesPerView: 1
            }
          }
        }
      }
    },
    created() {
      this.query="Peacock";
      this.getpictures();
    },
     components: {
      Swiper,
      SwiperSlide,
      slidePan
    },
    computed: {
      swiper() {
        return this.$refs.mySwiper.$swiper
      }
    },
    mounted() {
      console.log('Current Swiper instance object', this.swiper)
      this.swiper.slideTo(3, 1000, false)
    },
    methods: {
      getpictures: async function() {
        var url = "https://api.unsplash.com/search/photos/?client_id=KKRlvrZYsjvvtW85uFP372hg21TfqkopEgi3EevoASo&query="+this.query;
        try{
          this.resultpics = await this.axios.get(url).then(data => {
            console.log(data.data.results)
            return data.data.results
          })
        }
        catch(e) {
          console.log("exception");
          console.log(e);
        }
      },
      generatepdf(url,id) {
      var img = new Image();
      img.src = url;
        var doc = new jsPDF('p', 'pt', 'a4');
        doc.addImage(img, "PNG", 140, 10, 300, 300);
        doc.save(id+".pdf");
      },
      completepdf() {
        var doc = new jsPDF('p', 'pt', 'a4');
        var pageHeight= doc.internal.pageSize.height;
        var x=(doc.internal.pageSize.width - this.query.length-30)/2
        doc.text(this.query, x, 50)
        var y = 80 

        for(var i=0;i<this.resultpics.length;i++) {
            // Height position of new content
            if (y >= pageHeight)
            {
              doc.addPage();
              y = 80 // Restart height position
            }
           var img = new Image();
            img.src = this.resultpics[i].urls.thumb;
            doc.addImage(img, "PNG", 140, y, 300, 300);
            y=y+410;
        }
        
        doc.save(this.query+".pdf");
      }
    }
  }
</script> 
<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.header {
  padding: 10px 16px;
  background: #5fcf72;
  color: #f1f1f1;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%
}
* {
  box-sizing:border-box;
}

.piccard {
  width: 300px;
}
.resulthub {
  margin-top:100px
}

@media only screen and (max-width: 600px) {
  .example {
    width:100%;
    margin:auto;
  }
  .apptitle {
    display: block;
  }
  .piccard {
    width:100%
  }

  .resulthub {
      margin-top:150px
  }
   .button2{
   background: #b3b6b7c4; 
   min-width:120px;
  padding: 6px;
  max-height:47px;
  font-size: 10px;
 }
}

@media only screen and (min-width: 600px) {
  .example {
    width:80%;
    margin:auto;
  }
  .apptitle {
    width: 100%;
    text-align: center;
    display: block;
  }
     .button2{
   background: #b3b6b7c4; 
   min-width:120px;
  padding: 6px;
  max-height:47px;
  font-size: 10px;
 }
}

@media only screen and (min-width: 768px) {
  .example {
    width: 70%;
    margin: auto;
    display: inline;
    float: right;
  }
  .apptitle {
    width: 20%;
    display: inline;
    float: left;
  }
     .button2{
   background: #b3b6b7c4; 
   min-width:100px;
  padding: 6px;
  max-height:47px;
  font-size: 10px;
 }
}
@media only screen and (max-width: 768px) {
  .piccard {
    width:70%;
    margin: auto
  }
}
@media only screen and (min-width: 992px) {
  .example {
    width: 60%;
    margin: auto;
    display: inline;
    float: right;
  }
   .button2{
   background: #b3b6b7c4; 
   min-width:100px;
  padding: 6px;
  max-height:47px;
  font-size: 10px;
 }
  .apptitle {
    width: 20%;
    display: inline;
    float: left;
  }
}

@media only screen and (min-width: 1200px) {
  .example {
    width: 60%;
    margin: auto;
    display: inline;
    float: right;
  }
  .apptitle {
    width: 20%;
    display: inline;
    float: left;
  }
}

.example input[type=text] {
  display: block;
  padding: 10px;
  font-size: 17px;
  border: 1px solid grey;
  float: left;
  width: 60%;
  background: #f1f1f1;
}

.example .button1{
  display: block;
  float: left;
  width: 10%;
  padding: 10px;
  background: #b3b6b7c4;
  color: white;
  font-size: 17px;
  border: 1px solid grey;
  border-left: none;
  cursor: pointer;
}
 .button2{
   background: #b3b6b7c4; 
   width: 20%;
  padding: 10px;
  max-height:47px;
  font-size: 15px;
 }

.example button:hover {
  background:#b3b6b7c4;
}

.example::after {
  content: "";
  clear: both;
  display: table;
}
.swiper {
  height: 80vh;
  width: 100%;

  .swiper-slide {
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-weight: bold;
    background-color: white;
  }
}
</style>