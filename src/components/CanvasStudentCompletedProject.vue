<template>
  <div class="completed-assignment p-b2" style="background-color:#fff">
      <h1 class="color-p fs-b4 serif w-700 m-0-0-s4">Thank You!</h1>

      <div v-if="project.project.type == 'Project'" v-html="project.thanks" class="m-0-0-lg"></div>
      <p v-else>You've completed your assignment!</p>

      <template v-if="project.project.type == 'Project'">
      <div class="project-video m-0-0-lg">
        <div class="videoWrapper" style="height:auto">
          <video controls class="videoWrapper__video" :poster="project.thankyou_video_poster" preload="auto">
            <template v-for="url in project.thankyou_video_urls_filtered">
              <source :key="url" :src="url">
            </template>
          </video>
        </div>
      </div>
      </template>

      <template v-if="data_display">
        <div class="separator"></div>
        <h1 class="color-p fs-b4 serif w-700 m-0-0-s4">Explore Project Data</h1>
        <div>replace this div with data display</div>

      </template>
  </div>
</template>

<script>
export default {
  name: 'StudentCompletedProject',
  props: ['project','user','organization'],
  data: function(){
    return {
      data_display:true
    }
  },
  created(){
    // filter videos so no nulls
    let ctx = this
    ctx.project.thankyou_video_urls_filtered = [];
    ctx.project.thankyou_video_urls.forEach(function(v){
      if(v){ctx.project.thankyou_video_urls_filtered.push(v)}
    })
  }
}
</script>

<style lang="scss">

</style>
