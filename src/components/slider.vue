<template>
  <div>
  <div @click="prevSlide()" class="arrowwidth" style="display:inline-block;width:5%">
  <svg xmlns="http://www.w3.org/2000/svg" width="45" height="45" fill="currentColor" class="bi bi-arrow-left-circle-fill" viewBox="0 0 16 16">
  <path d="M8 0a8 8 0 1 0 0 16A8 8 0 0 0 8 0zm3.5 7.5a.5.5 0 0 1 0 1H5.707l2.147 2.146a.5.5 0 0 1-.708.708l-3-3a.5.5 0 0 1 0-.708l3-3a.5.5 0 1 1 .708.708L5.707 7.5H11.5z"/>
</svg>
  </div>
   <div class="panwidth" style="display:inline-block; width:90%">
    <slide v-for="i in slideIndex" :slidedata="slideDataCollection[i]" :key="i"></slide>
   </div>
    <div @click="nextSlide()" class="arrowwidth" style="display:inline-block; width:5%">
    <svg xmlns="http://www.w3.org/2000/svg" width="45" height="45" fill="currentColor" class="bi bi-arrow-right-circle-fill" viewBox="0 0 16 16">
  <path d="M8 0a8 8 0 1 1 0 16A8 8 0 0 1 8 0zM4.5 7.5a.5.5 0 0 0 0 1h5.793l-2.147 2.146a.5.5 0 0 0 .708.708l3-3a.5.5 0 0 0 0-.708l-3-3a.5.5 0 1 0-.708.708L10.293 7.5H4.5z"/>
</svg>
  </div>
  </div>
</template>

<script>
import slide from './slide.vue'
export default {
  name: 'slidePan',
  props: {
    msg: String,
    slideDataCollection: Array
  },
  data() {
    return {
        windowWidth:"",
        slidePerView:"",
        slideIndex:[]
    }
  },
  components: {
    slide
  },
  created() {
    this.windowWidth = window.innerWidth;
    this.slidePerView = 3;
    this.slideIndex =[0,1,2]
    this.setslides();
  },
    mounted() {
        this.$nextTick(() => {
            window.addEventListener('resize', this.setslides);
        })
    },
    methods: {  
        setslides() {
             this.windowWidth = window.innerWidth
            if(this.windowWidth > 950) {
              this.slidePerView = 3
            }
            if(this.windowWidth < 950 && this.windowWidth > 790)
            {
                 this.slidePerView = 2
            }
            if(this.windowWidth < 640 && this.windowWidth > 150)
            {
              this.slidePerView = 1
            }
            if(this.windowWidth < 150) 
            {
              this.slidePerView = 1
            }
            if(this.slideIndex.length<this.slidePerView) {
                for(var j=0; j<this.slidePerView-this.slideIndex.length; j++) {
                    var n=this.slideIndex.length;
                    this.slideIndex.push(this.slideIndex[n-1]+1);
                }        
            }
            if(this.slideIndex.length>this.slidePerView) {
                var m = this.slideIndex.length-this.slidePerView
                for(var k=0; k<m; k++) {
                    this.slideIndex.pop();
                }        
            }

        },
        nextSlide() {
            if(this.slideIndex[this.slidePerView-1] < this.slideDataCollection.length) {
               this.slideIndex = this.slideIndex.map(x => x+this.slidePerView) 
            }
        },
        prevSlide() {
            if(this.slideIndex[0]>0) {
                this.slideIndex = this.slideIndex.map(x => x - this.slidePerView)
            }
        }
    },
    beforeDestroy() { 
        window.removeEventListener('resize', this.setslides); 
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.panwidth {
width:90%
}
.arrowwidth {
width:2%
}
@media only screen and (max-width: 1180px) { 
}
</style>
