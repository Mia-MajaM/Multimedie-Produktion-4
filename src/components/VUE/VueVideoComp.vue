<script setup></script>
<template>
  <!-- // IMG POSTER SOURCE = Oplevskansen.dk -->
  <video controls poster="/src/assets/img/fitness/Video-poster.png">
    <source src="../../assets/video/Skansens_video_edited.mp4" type="video/mp4" />
    Your browser does not support the video tag.
  </video>
</template>
<style scoped lang="scss">
@import "../../css/style.scss";
video {
  height: 80%;
  width: 80%;
  max-width: 1440px;
  border-radius: $box-border-section;
  background-color: $davys-gray;
}
</style>
