---
layout: page
title: gallery
permalink: /gallery/
nav: true
nav_order: 5
---

<style>
.gallery-item {
  position: relative;
  overflow: hidden;
}

.gallery-item img {
  width: 100%;
  border-radius: 8px;
  transition: transform .3s ease;
  cursor: zoom-in;
}

.gallery-item:hover img {
  transform: scale(1.03);
}

.gallery-item .caption {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 8px 10px;
  text-align: center;
  background: rgba(0,0,0,0.65);
  color: white;
  opacity: 0;
  transition: opacity .2s ease;
  font-size: 0.9rem;
}

.gallery-item:hover .caption {
  opacity: 1;
}
</style>

<div class="container">
  <div class="row">

    <!-- image 1 -->
    <div class="col-md-4 col-sm-6 mb-4">
      <a data-toggle="modal" data-target="#imgModal">
        <div class="gallery-item">
          <img class="img-fluid rounded"
               src="/assets/img/2023-vitreorana_parvula.jpg"
               alt="Vitreorana parvula">
          <div class="caption">Vitreorana parvula (BR: SP: Boraceia, 2023)</div>
        </div>
      </a>
    </div>

    <!-- image 2 -->
    <div class="col-md-4 col-sm-6 mb-4">
      <a data-toggle="modal" data-target="#imgModal">
        <div class="gallery-item">
          <img class="img-fluid rounded"
               src="/assets/img/2023-brachycephalus_margaritatus.jpg"
               alt="Brachycephalus margaritatus">
          <div class="caption">Brachycephalus margaritatus (BR: RJ: Petrópolis, 2023)</div>
        </div>
      </a>
    </div>

  </div>
</div>

<!-- Bootstrap 4 modal -->
<div class="modal fade" id="imgModal" tabindex="-1">
  <div class="modal-dialog modal-dialog-centered modal-lg">
    <div class="modal-content bg-transparent border-0">
      <img id="modalImage" class="img-fluid rounded">
    </div>
  </div>
</div>

<script>
document.addEventListener("click", function(e){
  if(e.target.tagName === "IMG" && e.target.closest(".gallery-item")){
    document.getElementById("modalImage").src = e.target.src;
  }
});
</script>

