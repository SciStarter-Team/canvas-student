<template>
<div id="app">

  <!-- ******** REMOVE FOR PRODUCTION *********** -->
  <!-- <div style="position:fixed;top:0;right:0;background-color:rgba(255,255,255,0.9);max-width:300px;padding:10px"> -->
    <!--     <p><a @click="changeUserType('student')">View As Student (default)</a></p> -->
    <!--     <p><a @click="changeTeacherContext('assignment')">View As Teacher when viewing on their assignment page (and after using external tool to assign)</a></p> -->
    <!--     <p><a @click="changeTeacherContext('student')">View As Teacher when viewing individual student submissions (in speedgrader)</a></p> -->
    <!--     <hr /> -->
    <!--     <p><a @click="changeProject(fakeData.items[0])">View Affiliate (default)</a></p> -->
    <!--     <p><a @click="changeProject(fakeData.items[8])">View Project with SciStarter Hosted Form</a></p> -->
    <!--     <p><a @click="changeProject(fakeData.custom[2])">View Teacher Created Project</a></p> -->
    <!--     <hr /> -->
    <!--     <p><a @click="changeUserAccount(false)">View Do Project as Student who has NOT created a SS account  (default)</a></p> -->
    <!--     <p><a @click="changeUserAccount(true)">View Do Project as Student who has created a SS account</a></p> -->
    <!--     <hr /> -->
    <!--     <p><a @click="changeAssignmentSetting('student')">Student submits data (default)</a></p> -->
    <!--     <p><a @click="changeAssignmentSetting('teacher')">Teacher submits data</a></p> -->
    <!--     <hr /> -->
    <!-- </div> -->
  <!-- ******************************************-->

  <div class="ss-canvas-wrapper canvas-style">
    <header>
      <img src="./assets/img/canvas/scistarter-logo-web-r.svg" alt="SciStarter logo" />
    </header>
    <main>

      <!-- THIS IS THE STUDENT ONLY VIEW -->
      <StudentWrapper v-if="user.type==='student'" :user="user" :organization="organization" :project="project" :assignment_settings="assignment_settings" :assignment="assignment" :direct="direct_input"/>

      <!-- THIS IS THE TEACHER ONLY VIEW -->
      <TeacherWrapper v-if="user.type==='teacher'" :user="user" :organization="organization" :project="project" :teacher_context="teacher_context" :assignment="assignment" :direct="direct_input"/>

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

    computed: {
        assignment() {
            return JSON.parse(document.getElementById('data-assignment').textContent);
        },

        direct_input() {
            return this.assignment.direct_input;
        },

        organization() {
            return JSON.parse(document.getElementById('data-institution').textContent);
        },

        user() {
            return JSON.parse(document.getElementById('data-user').textContent);
        },

        project() {
            return JSON.parse(document.getElementById('data-project').textContent);
        },

        assignment_settings() {
            return {
                id: this.assignment.id,
                submitted_by: this.direct_input ? 'student' : 'teacher'
            };
        }
    },

    data: function() {
        return {
            teacher_context: 'assignment', // two possible values:
            // 'assignment' is the view after a teacher uses the external tool to assign or goes back to the
            // assignment page in Canvas
            // 'student' is when the teacher is viewing the idividual submissions in speedgrader
            student_viewed: 'ChuckNorris@broward.edu', // student viewed when context is 'student'

            fakeData: store.broward,// this is the data you sent me about projects just for prototyping REMOVE

            assignment_submission: store.assignment_submission,
            // see example in store.js
            // this is a single submission object that you should get in the context of 'student' unless you just want to feed all the submissions
            assignment_submissions: [ // an array of submissions

            ]

        }
    },
    methods: {
        changeUserType(t) {
            this.user.type = t;
        },
        changeTeacherContext(v){
            this.user.type = 'teacher';
            this.teacher_context = v
        },
        changeProject(p) {
            this.project = p;
        },
        changeUserAccount(v) {
            this.user.hasAccount = v
    },
    changeAssignmentSetting(v) {
      this.assignment_settings.submitted_by = v
    }
  }
}
</script>

<style lang="scss">
@import './assets/css/CanvasVariables.scss';
@import './assets/css/CanvasApp.scss';

</style>
