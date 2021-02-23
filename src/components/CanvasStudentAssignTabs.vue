<template>
  <div id="canvas-tabs" class="student">

    <!-- TAB NAVIGATION -->
    <ul class="canvas-tab-nav" role="tablist"  aria-label="Tabs" ref="tabs">
        <li><a @click.prevent="selectTab(0)" role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1" tabindex="0" href="#panel-1" class="selected">
            <div class="ss-tab-icon"><img v-if="completed" src="../assets/img/canvas/high-five.svg" alt="high five icon"><img v-else src="../assets/img/canvas/robot-head.svg" alt="SciStarter icon" /></div>
            <div class="ss-tab-text">
              <span v-if="completed">Thank You</span>
              <span v-else>Get Started</span>
            </div>
        </a></li>
        <li><a @click.prevent="selectTab(1)" role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1" href="#panel-2">
          <div class="ss-tab-icon"><img src="../assets/img/canvas/view-project.svg" alt="television icon" /></div>
          <div class="ss-tab-text">
              <span>About the project</span>
          </div>
        </a></li>
        <li><a @click.prevent="selectTab(2)" role="tab" aria-selected="false" aria-controls="panel-3" id="tab-3" tabindex="-1" href="#panel-3">
          <div class="ss-tab-icon"><img src="../assets/img/canvas/do-project.svg" alt="paper icon" /></div>
          <div class="ss-tab-text">
            <span v-if="assignment.input_mode=='worksheet'">Go to Worksheet</span>
            <span v-else>Do the Project</span>
          </div>
        </a></li>
      </ul>

      <!-- TAB PANELS -->

      <div id="panel-1" class="canvas-panel" v-if="tabIndex === 0" role="tabpanel" tabindex="0" aria-labelledby="tab-1" >
        <keep-alive>
          <StudentCompletedProject v-if="completed" :project="project" :user="user" :organization="organization" :direct="direct" :assignment="assignment"/>
          <StudentWelcome v-else :project="project" :user="user" :organization="organization" />
      </keep-alive>
      </div>



      <div id="panel-2" class="canvas-panel" v-if="tabIndex === 1" role="tabpanel" tabindex="0" aria-labelledby="tab-2">
        <keep-alive>
        <StudentViewProject :project="project" :user="user" :organization="organization" :direct="direct" :assignment_settings="assignment_settings" @triggerTab="handleTabTriggerEmit" />
      </keep-alive>
      </div>


      <div id="panel-3" class="canvas-panel"  v-if="tabIndex === 2" role="tabpanel" tabindex="0" aria-labelledby="tab-3">
        <keep-alive>
        <StudentDoProject :project="project" :user="user" :organization="organization" :assignment_settings="assignment_settings"  :direct="direct" v-on="$listeners" />
      </keep-alive>
      </div>

  </div>
</template>

<script>
import StudentWelcome from '../components/CanvasStudentWelcome.vue'
import StudentViewProject from '../components/CanvasStudentViewProject.vue'
import StudentDoProject from '../components/CanvasStudentDoProject.vue'
import StudentCompletedProject from '../components/CanvasStudentCompletedProject'

export default {
  name: 'StudentAssignmentTabs',
  components: {
    StudentWelcome,
    StudentViewProject,
    StudentDoProject,
    StudentCompletedProject,
  },
  props: ['completed', 'project','user','organization','assignment_settings', 'assignment', 'direct'],
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
    },
    handleTabTriggerEmit(e) {
      this.selectTab(e)
    }
  }
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasTeacherAssignTabs.scss';
</style>
