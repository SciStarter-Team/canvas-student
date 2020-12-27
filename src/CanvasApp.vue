<template>
  <div id="app">

      <!-- ******** REMOVE FOR PRODUCTION *********** -->
        <div style="position:fixed;top:0;right:0;background-color:rgba(255,255,255,0.9);max-width:300px;padding:10px">
            <p><a @click="changeUserType('student')">View As Student (default)</a></p>
            <p><a @click="changeUserType('teacher')">View As Teacher</a></p>
            <hr />
            <p><a @click="changeProject(fakeData.items[0])">View Affiliate (default)</a></p>
            <p><a @click="changeProject(fakeData.items[8])">View Project with SciStarter Hosted Form</a></p>
            <p><a @click="changeProject(fakeData.custom[2])">View Teacher Created Project</a></p>
            <hr />
            <p><a @click="changeUserAccount(false)">View Do Project as Student who has NOT created a SS account  (default)</a></p>
            <p><a @click="changeUserAccount(true)">View Do Project as Student who has created a SS account</a></p>
        </div>
      <!-- ******************************************-->

      <div class="ss-canvas-wrapper canvas-style">
        <header>
            <img src="./assets/img/canvas/scistarter-logo-web-r.svg" alt="SciStarter logo" />
        </header>
        <main>

          <!-- THIS IS THE STUDENT ONLY VIEW -->
          <StudentWrapper v-if="user.type==='student'" :user="user" :organization="organization" :project="project" />

          <!-- THIS IS THE TEACHER ONLY VIEW -->
          <TeacherWrapper v-if="user.type==='teacher'" :user="user" :organization="organization" :project="project" />


        </main>
        <footer>
        </footer>
      </div><!-- end .ss-canvas-wrapper-->

  </div>
</template>

<script>
//***** REMOVE FOR PRODUCTION ********/
import store from './store.js'
//************************************
import StudentWrapper from './components/CanvasStudentWrapper'
import TeacherWrapper from './components/CanvasTeacherWrapper'
export default {
  name: 'App',
  components: {
    StudentWrapper,
    TeacherWrapper
  },
  data: function(){
    return {
      organization: {
        name: 'Broward',
        display_name: 'Broward’s PBL Portal @SciStarter'
      }, // replace with real data
      user: {
        "id":69,
        "name":"kevinripka",
        "school":"",
        "type": "student",
        "email": 'ChuckNorris@broward.edu',
        hasAccount: false
      }, // replace with real data
      project: store.broward.items[0],
      fakeData: store.broward
    }
  },
  methods: {
    changeUserType(t) {
      this.user.type = t;
    },
    changeProject(p) {
      this.project = p;
    },
    changeUserAccount(v) {
      this.user.hasAccount = v
    }
  }
}
</script>

<style lang="scss">
@import './assets/css/CanvasVariables.scss';
@import './assets/css/CanvasApp.scss';

</style>
