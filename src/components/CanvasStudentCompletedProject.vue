<template>
<div class="completed-assignment p-b2" style="background-color:#fff">
  <h1 class="color-b fs-b4 serif w-700 m-0-0-s4">Thank You!</h1>

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

  <div v-if="project.project.type == 'Project'" v-html="project.thanks" class="m-0-0-lg"></div>
  <p v-else-if="user.external_view">Thank you for contributing to {{project.project.name}}!</p>
  <p v-else>You've completed your assignment!</p>

  <p>
    Find more citizen science projects that need your help on <a v-if="user.hasAccount" target="_blank" href="https://scistarter.org/">SciStarter.org</a><a v-else @click="signup_dialog_visible=true">SciStarter.org</a>!
  </p>

  <p v-if="project.get_data_url">
    The data that this project has collected can be <a :href="project.get_data_url" target="_blank">downloaded here</a>.
  </p>

  <template v-if="assignment.mappable">
    <p>
      Here's a map of other people's contributions to
      {{project.project.name}}, near you and around the world. Nearby
      contributions are combined into one marker. Zoom in to see more!
    </p>
    <Map :data-url="assignment.map_url"/>
  </template>

  <!-- <template v-if="data_display && project.project.type==='Project'"> -->
    <!--   <div class="separator"></div> -->
    <!--   <h1 class="color-b fs-b4 serif w-700 m-0-0-s4">Explore Project Data</h1> -->
    <!--   <div>replace this div with data display</div> -->

    <!-- </template> -->
  <el-dialog title="Create a SciStarter Account" :visible.sync="signup_dialog_visible" width="400px">
    <JoinSciStarter @close="signup_dialog_visible=false" :user="user" next="https://scistarter.org/"/>
  </el-dialog>
</div>
</template>

<script>
import Map from '../components/CanvasMap.vue'
import JoinSciStarter from '../components/CanvasJoinSciStarter.vue'

export default {
    name: 'StudentCompletedProject',
    props: ['project','user','organization', 'direct', 'assignment'],
    components: {
        Map,
        JoinSciStarter
    },
    data: function(){
        return {
            data_display:true,
            signup_dialog_visible: false,
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
    },
}
</script>

<style lang="scss">

</style>
