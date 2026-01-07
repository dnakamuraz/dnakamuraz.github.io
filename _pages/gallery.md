---
layout: page
title: gallery
permalink: /gallery/
---

<style>
.masonry {
  column-count: 3;
  column-gap: 1em;
}
.masonry-item {
  break-inside: avoid;
  margin-bottom: 1em;
}
.masonry-item img {
  width: 100%;
  border-radius: 8px;
  cursor: pointer;
}
@media (max-width: 768px) {
  .masonry {
    column-count: 1;
  }
}
</style>

<div class="masonry">

  <!-- IMAGE 1 -->
  <div class="masonry-item">
    <img src="/assets/img/2023-vitreorana_parvula.jpg"
         data-bs-toggle="modal"
         data-bs-target="#img1">
    <p class="text-center">
      Vitreorana parvula (BR: SP: Boraceia – 2023)
    </p>
  </div>

  <!-- IMAGE 2 -->
  <div class="masonry-item">
    <img src="/assets/img/2023-brachycephalus_margaritatus.jpg"
         data-bs-toggle="modal"
         data-bs-target="#img2">
    <p class="text-center">
      Brachycephalus margaritatus (BR: RJ: Petrópolis – 2023)
    </p>
  </div>

</div>

<!-- MODAL 1 -->
<div class="modal fade" id="img1" tabindex="-1">
  <div class="modal-dialog modal-dialog-centered modal-lg">
    <div class="modal-content">
      <img src="/assets/img/2023-vitreorana_parvula.jpg">
      <div class="modal-body">
        <strong>Vitreorana parvula</strong><br>
        BR: SP: Boraceia – 2023
      </div>
    </div>
  </div>
</div>

<!-- MODAL 2 -->
<div class="modal fade" id="img2" tabindex="-1">
  <div class="modal-dialog modal-dialog-centered modal-lg">
    <div class="modal-content">
      <img src="/assets/img/2023-brachycephalus_margaritatus.jpg">
      <div class="modal-body">
        <strong>Brachycephalus margaritatus</strong><br>
        BR: RJ: Petrópolis – 2023
      </div>
    </div>
  </div>
</div>

