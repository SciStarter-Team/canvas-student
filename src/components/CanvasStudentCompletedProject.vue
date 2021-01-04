<template>
<div class="completed-assignment p-b2" style="background-color:#fff">
  <h1 class="color-b fs-b4 serif w-700 m-0-0-s4">Thank You!</h1>

  <div v-if="project.project.type == 'Project'" v-html="project.thanks" class="m-0-0-lg"></div>
  <p v-else>You've completed your assignment!</p>

  <template v-if="project.project.type == 'Project'">
    <div class="project-video m-0-0-lg">
      <div class="videoWrapper" style="height:auto">
        <video controls class="videoWrapper__video" :poster="thanks_poster" preload="auto">
          <template v-for="vid in thanks_videos">
            <source :key="vid.url" :type="vid.type" :src="vid.url">
          </template>
        </video>
      </div>
    </div>
  </template>

  <template v-if="data_display && project.project.type==='Project'">
    <div class="separator"></div>
    <h1 class="color-b fs-b4 serif w-700 m-0-0-s4">Explore Project Data</h1>
    <div>replace this div with data display</div>

  </template>
</div>
</template>

<script>
export default {
    name: 'StudentCompletedProject',
    props: ['project','user','organization', 'direct'],
    data: function(){
        return {
            data_display:true
        }
    },
    computed: {
        thanks_videos() {
            return this.project.videos.filter(vid => vid.role === 1);
        },

        thanks_poster() {
            // return this.thanks_videos.find(vid => !!vid.poster).poster || '';
            var matches = this.thanks_videos.filter(function(v) { return !!v.poster});
            if(matches.length >= 1) {
                return matches[0].poster;
            }
            else {
                return '';
            }
        },
    }
}
</script>

<style lang="scss">

</style>
