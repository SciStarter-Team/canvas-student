<template>
<div id="app">
  <div class="ss-canvas-wrapper canvas-style">
    <header>
      <img src="./assets/img/canvas/scistarter-logo-web-r.svg" alt="SciStarter logo" />
      <el-dropdown trigger="click" @command="help" class="help-menu">
        <img class="trigger" src="./assets/img/canvas/hamburger.svg" alt="open help and navigation menu" />
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item icon="el-icon-question" command="faq">Frequently Asked Questions</el-dropdown-item>
          <el-dropdown-item icon="el-icon-info" command="intro">Intro to Citizen Science</el-dropdown-item>
          <el-dropdown-item icon="el-icon-s-help" command="howto" v-if="user.type==='teacher'">How to Pick or Create a New Project</el-dropdown-item>
          <!-- <el-dropdown-item icon="el-icon-s-data" command="data">Data Explorer</el-dropdown-item> -->
          <el-dropdown-item icon="el-icon-s-opportunity" command="scistarter">Open the SciStarter Web Site</el-dropdown-item>
          <el-dropdown-item icon="el-icon-s-custom" command="dashboard" v-if="user.hasAccount">SciStarter Dashboard</el-dropdown-item>
        </el-dropdown-menu>
    </el-dropdown>
    </header>
    <main>
      <!-- THIS IS THE STUDENT ONLY VIEW -->
      <StudentWrapper v-if="user.type==='student'" :user="user" :organization="organization" :project="project" :assignment_settings="assignment_settings" :assignment="assignment" :direct="direct_input"/>

      <!-- THIS IS THE TEACHER ONLY VIEW -->
      <TeacherWrapper v-if="user.type==='teacher'" :user="user" :organization="organization" :project="project" :teacher_context="teacher_context" :assignment="assignment" :direct="direct_input"/>

      <el-dialog title="Frequently Asked Questions" :visible.sync="faq_dialog_visible">
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut sollicitudin aliquam lorem nec fermentum. Nunc ullamcorper ligula at fringilla ultrices. Quisque vitae eros euismod, euismod felis sed, semper ligula. Vivamus consequat dignissim accumsan. Maecenas sed lectus bibendum, euismod dolor at, facilisis ex. Quisque iaculis gravida vulputate. Suspendisse id condimentum dolor. Vestibulum vestibulum pulvinar posuere. Suspendisse eget hendrerit risus. Nunc at dolor eu lorem dictum tincidunt vitae at erat. Proin quam mauris, ultrices a molestie et, blandit sit amet lacus. Curabitur tempor sed sapien eu tristique.</p>
        <p>Vestibulum ligula diam, porttitor at enim non, rhoncus cursus lorem. Proin id justo sit amet dui euismod iaculis sed aliquet augue. Vivamus convallis gravida augue sed aliquet. Phasellus auctor mollis elit ut pellentesque. Proin vel enim enim. Duis efficitur venenatis lorem, in facilisis turpis sodales mollis. In tincidunt lacus ipsum, quis ultricies elit pretium a. Donec vitae nulla eu est laoreet aliquam quis ut tortor. Sed fringilla nunc at metus tempus, ac consectetur risus fringilla. Pellentesque pellentesque eu felis at mattis. Sed in est pulvinar, faucibus dolor eget, finibus ante. Praesent rutrum, leo ut fermentum commodo, sapien justo sagittis massa, a luctus ante ante vitae ante.</p>
      </el-dialog>

      <el-dialog title="Intro to Citizen Science" :visible.sync="intro_dialog_visible" width="580px">
        <iframe width="540" height="460" src="https://orrery-media.s3-us-west-2.amazonaws.com/Tutorials/Intro+to+Citizen+Science+General-Bilingual+12142020/story.html"></iframe>
      </el-dialog>

      <el-dialog title="How to Pick or Create a New Project" :visible.sync="howto_dialog_visible">
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut sollicitudin aliquam lorem nec fermentum. Nunc ullamcorper ligula at fringilla ultrices. Quisque vitae eros euismod, euismod felis sed, semper ligula. Vivamus consequat dignissim accumsan. Maecenas sed lectus bibendum, euismod dolor at, facilisis ex. Quisque iaculis gravida vulputate. Suspendisse id condimentum dolor. Vestibulum vestibulum pulvinar posuere. Suspendisse eget hendrerit risus. Nunc at dolor eu lorem dictum tincidunt vitae at erat. Proin quam mauris, ultrices a molestie et, blandit sit amet lacus. Curabitur tempor sed sapien eu tristique.</p>
        <p>Vestibulum ligula diam, porttitor at enim non, rhoncus cursus lorem. Proin id justo sit amet dui euismod iaculis sed aliquet augue. Vivamus convallis gravida augue sed aliquet. Phasellus auctor mollis elit ut pellentesque. Proin vel enim enim. Duis efficitur venenatis lorem, in facilisis turpis sodales mollis. In tincidunt lacus ipsum, quis ultricies elit pretium a. Donec vitae nulla eu est laoreet aliquam quis ut tortor. Sed fringilla nunc at metus tempus, ac consectetur risus fringilla. Pellentesque pellentesque eu felis at mattis. Sed in est pulvinar, faucibus dolor eget, finibus ante. Praesent rutrum, leo ut fermentum commodo, sapien justo sagittis massa, a luctus ante ante vitae ante.</p>
      </el-dialog>
    </main>
    <footer>
    </footer>
  </div><!-- end .ss-canvas-wrapper-->

</div>
</template>

<script>
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
            faq_dialog_visible: false,
            intro_dialog_visible: false,
            howto_dialog_visible: false,

            teacher_context: 'assignment', // two possible values:
            // 'assignment' is the view after a teacher uses the external tool to assign or goes back to the
            // assignment page in Canvas
            // 'student' is when the teacher is viewing the idividual submissions in speedgrader
            student_viewed: 'ChuckNorris@broward.edu', // student viewed when context is 'student'

            fakeData: null,

            assignment_submission: null,
            // see example in store.js
            // this is a single submission object that you should get in the context of 'student' unless you just want to feed all the submissions
            assignment_submissions: [ // an array of submissions

            ]

        }
    },
    methods: {
        help(command) {
            switch(command) {
            case "faq":
                this.faq_dialog_visible = true;
                break;
            case "intro":
                this.intro_dialog_visible = true;
                break;
            case "howto":
                this.howto_dialog_visible = true;
                break;
            case "data":
                break;
            case "scistarter":
                window.open('https://scistarter.org/', '_blank');
                break;
            case "dashboard":
                window.open('https://scistarter.org/dashboard', '_blank');
                break;
            default:
            }
        },
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

header {
    position: relative;

    .help-menu.el-dropdown {
        position: absolute;
        top: 28px;
        right: 150px;
        border: none;
        padding: 0px;
    }
}
</style>
