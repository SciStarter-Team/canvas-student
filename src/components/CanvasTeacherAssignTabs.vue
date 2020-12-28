<template>
  <div id="canvas-tabs">

    <!-- TAB NAVIGATION -->
    <ul class="canvas-tab-nav" :class="{'ctab-two':project.project.type==='CustomProject'}" role="tablist"  aria-label="Tabs" ref="tabs">
        <li><a @click.prevent="selectTab(0)" role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1" tabindex="0" href="#panel-1" class="selected">
            <div class="ss-tab-icon"><img src="../assets/img/canvas/pointing-hand.svg" alt="pointing hand icon" /></div>
            <div class="ss-tab-text">
                <span>Review Selected Project</span>
            </div>
        </a></li>
        <li><a @click.prevent="selectTab(1)" role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1" href="#panel-2">
          <div class="ss-tab-icon"><img src="../assets/img/canvas/screwdriver.svg" alt="screwdriver icon" /></div>
          <div class="ss-tab-text">
              <span>Download Student Submissions</span>
          </div>
        </a></li>

        <li v-if="project.project.type==='Project'"><a @click.prevent="selectTab(2)" role="tab" aria-selected="false" aria-controls="panel-3" id="tab-3" tabindex="-1" href="#panel-3">
          <div class="ss-tab-icon"><img src="../assets/img/canvas/screwdriver.svg" alt="screwdriver icon" /></div>
          <div class="ss-tab-text">
              <span>Thank You Video</span>
          </div>
        </a></li>

      </ul>

      <!-- TAB PANELS -->

      <div id="panel-1" class="canvas-panel" v-if="tabIndex === 0" role="tabpanel" tabindex="0" aria-labelledby="tab-1" >
        <keep-alive>
        <ReviewProject  :user="user" :organization="organization" :project="project" />
      </keep-alive>
      </div>

      <div id="panel-2" class="canvas-panel" v-if="tabIndex === 1" role="tabpanel" tabindex="0" aria-labelledby="tab-2">
        <keep-alive>
        <ReviewSubmissions :user="user" :organization="organization" />
      </keep-alive>
      </div>

      <div id="panel-3" class="canvas-panel" v-if="tabIndex === 2" role="tabpanel" tabindex="0" aria-labelledby="tab-2">
        <keep-alive>
        <StudentCompletedProject :user="user" :organization="organization" :project="project" />
      </keep-alive>
      </div>


  </div>
</template>

<script>
import ReviewProject from '../components/CanvasTeacherReviewProject.vue'
import ReviewSubmissions from '../components/CanvasTeacherReviewSubmissions.vue'
import StudentCompletedProject from '../components/CanvasStudentCompletedProject.vue'
export default {
  components: {
    ReviewProject,
    ReviewSubmissions,
    StudentCompletedProject
  },
  props: ['user','organization','project','teacher_context'],
  data: function(){
    return {
      tabIndex: 0
    }
  },
  methods: {
    selectTab(i){
      if (i !== this.tabIndex){
        let tabs = this.$refs.tabs.querySelectorAll('a')
        tabs.forEach(function(d){
          d.classList.remove('selected')
        })
        tabs.item(i).classList.add('selected')
        this.tabIndex = i
        window.scrollTo(0,0)
      }
    }
  }
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasTeacherAssignTabs.scss';
</style>
