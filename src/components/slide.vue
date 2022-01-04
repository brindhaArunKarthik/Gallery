<template>
  <div style="display:inline-block" class="slidecard">
  <div class="card">
  <img class="card-img-top" :src="slidedata.urls.thumb" style="height:250px" alt="Card image cap">
  <ul class="list-group list-group-flush" style="text-align:left">
    <li class="list-group-item">Likes : {{slidedata.urls.likes}}</li>
    <li class="list-group-item">Created at:{{slidedata.urls.created_at}}</li>
    <li class="list-group-item">Photographer : {{slidedata.user.first_name}}</li>
  </ul>
  <div class="card-body">
    <button @click="generatepdf(slidedata.urls.thumb, slidedata.id)" class="card-link" style="cursor:arrow">download</button>
  </div>
  </div>
  </div>
</template>

<script>
import jsPDF from 'jspdf'
export default {
  name: 'slide',
  props: {
    slidedata: Object
  },
  methods: {  
    generatepdf(url,id) {
        var img = new Image();
        img.src = url;
            var doc = new jsPDF('p', 'pt', 'a4');
            doc.addImage(img, "PNG", 140, 10, 300, 300);
            doc.save(id+".pdf");
    }
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .slidecard {
        width:300px;
        margin:20px
    }
@media only screen and (max-width: 1180px) {
    .slidecard {
        width:250px
    }

}
</style>
