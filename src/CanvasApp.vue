<template>
<div id="app">
  <div class="ss-canvas-wrapper canvas-style">
    <header>
      <img src="./assets/img/canvas/scistarter-logo-web-r.svg" alt="SciStarter logo" />
      <el-dropdown v-if="user.via_school" trigger="click" @command="help" class="help-menu">
        <img class="trigger" src="./assets/img/canvas/hamburger.svg" alt="open help and navigation menu" />
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item icon="el-icon-question" command="faq">Frequently Asked Questions</el-dropdown-item>
          <el-dropdown-item icon="el-icon-info" command="intro">Intro to Citizen Science</el-dropdown-item>
          <el-dropdown-item icon="el-icon-s-help" command="howto" v-if="user.type==='teacher'">How to Pick or Create a New Project</el-dropdown-item>
          <!-- <el-dropdown-item icon="el-icon-s-data" command="data">Data Explorer</el-dropdown-item> -->
          <el-dropdown-item icon="el-icon-s-opportunity" command="scistarter">Open the SciStarter Web Site</el-dropdown-item>
          <el-dropdown-item icon="el-icon-s-custom" command="dashboard" v-if="user.hasAccount">SciStarter Dashboard</el-dropdown-item>
          <el-dropdown-item icon="el-icon-user-solid" command="password" v-if="user.type==='teacher'">Set SciStarter Password</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <main>
      <!-- THIS IS THE STUDENT ONLY VIEW -->
      <StudentWrapper v-if="user.type==='student'" :user="user" :organization="organization" :project="project" :assignment_settings="assignment_settings" :assignment="assignment" :direct="direct_input"/>

      <!-- THIS IS THE TEACHER ONLY VIEW -->
      <TeacherWrapper v-if="user.type==='teacher'" :user="user" :organization="organization" :project="project" :teacher_context="teacher_context" :assignment="assignment" :direct="direct_input"/>

      <el-dialog title="Frequently Asked Questions" :visible.sync="faq_dialog_visible" width="600px">
        <Faq slug="broward-demo"/>
      </el-dialog>

      <el-dialog title="Intro to Citizen Science" :visible.sync="intro_dialog_visible" width="580px">
        <iframe ref="tutframe" width="540" height="460" @load="$refs.tutframe.style.transform = 'scale(0.99, 1)'" src="https://media.scistarter.org/curated/Broward+Tutorial_January+2021/Broward+Citizen+Science+Tutorial_January_21_2021+-+Storyline+output/story.html"></iframe>
        <p>Or, <a href="https://media.scistarter.org/curated/Broward+Tutorial_January+2021/Broward+Citizen+Science+Tutorial_January_21_2021+-+Storyline+output/story.html" target="_blank">Open the tutorial in a new tab</a></p>
      </el-dialog>

      <el-dialog title="How to Pick or Create a New Project" :visible.sync="howto_dialog_visible">
        <p>In Canvas, create a new assignment or click the "Edit
      </el-dialog>

      <el-dialog title="Create a SciStarter Account" :visible.sync="signup_dialog_visible" width="400px">
        <form class="createAccount" @submit.prevent="createAccount">
          <label class="label">School email</label>
          <strong>{{user.email}}</strong>
          <label class="label">Password</label>
          <input type="password" v-model="signup_password"/>
          <a style="margin-right: 1rem" @click="cancelCreateAccount">skip</a><button type="submit" class="cbtn-primary"><i ref="join_spinner"></i>Create SciStarter Account</button>
        </form>
      </el-dialog>

      <el-dialog title="Set SciStarter Password" :visible.sync="password_dialog_visible" width="400px">
        <form class="createAccount" @submit.prevent="createAccount">
          <label class="label">School email</label>
          <strong>{{user.email}}</strong>
          <label class="label">Password</label>
          <input type="password" v-model="signup_password"/>
          <button type="submit" class="cbtn-primary"><i ref="join_spinner"></i>Set Password</button>
        </form>
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
import Faq from './components/CanvasFaq'
export default {
    name: 'App',

    components: {
        StudentWrapper,
        TeacherWrapper,
        Faq
    },

    computed: {
        assignment() {
            return JSON.parse(document.getElementById('data-assignment').textContent);
        },

        direct_input() {
            return this.assignment.direct_input || this.assignment.input_mode == 'delegated';
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
            let submitter = 'teacher';

            if(this.assignment.input_mode == 'direct') {
                submitter = 'student';
            }
            else if(this.assignment.input_mode == 'delegated') {
                submitter = 'delegated';
            }

            return {
                id: this.assignment.id,
                submitted_by: submitter,
                project_username: this.assignment.project_username,
                project_password: this.assignment.project_password,
                input_mode: this.assignment.input_mode
            };
        },

        xcsrftoken() {
            return JSON.parse(document.getElementById('data-xcsrf-token').textContent);
        }
    },

    data: function() {
        return {
            faq_dialog_visible: false,
            intro_dialog_visible: false,
            howto_dialog_visible: false,
            signup_dialog_visible: false,
            password_dialog_visible: false,

            signup_email: "",
            signup_password: "",

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
                if(this.user.hasAccount) {
                    window.open('https://scistarter.org/affiliates', '_blank');
                }
                else {
                    this.signup_dialog_visible = true;
                }
                break;
            case "dashboard":
                window.open('https://scistarter.org/dashboard', '_blank');
                break;
            case "password":
                this.password_dialog_visible = true;
                break;
            default:
            }
        },
        createAccount: function() {
            var ctx = this;

            var email = ctx.signup_email ? ctx.signup_email : ctx.user.email;

            var body = new FormData();
            body.append("email", email);
            body.append("password", ctx.signup_password);

            ctx.$refs.join_spinner.classList.add("spinner-border", "spinner-border-sm");

            fetch(ctx.user.create_account, {
                method: "POST",
                credentials: "include",
                headers: {"X-XCSRFToken": ctx.xcsrftoken},
                body: body
            }).then(response => response.json()).then(function(data) {
                ctx.$refs.join_spinner.classList.remove("spinner-border", "spinner-border-sm");

                if(data.result == 'created' || data.result == 'linked') {
                    ctx.user.email = data.email;
                    ctx.user.hasAccount = true;

                    if(data.warning) {
                        alert(data.warning);
                    }
                    else {
                        if(ctx.signup_dialog_visible) {
                            ctx.signup_dialog_visible = false;
                            window.open('https://scistarter.org/affiliates', '_blank');
                        }
                        else if(ctx.password_dialog_visible) {
                            ctx.password_dialog_visible = false;
                            alert("Password has been changed");
                        }
                    }
                }
                else {
                    alert(data.error);
                }
            });
        },
        cancelCreateAccount() {
            this.signup_dialog_visible = false;
            window.open('https://scistarter.org/affiliates', '_blank');
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
        top: 33px;
        right: 150px;
        border: none;
        padding: 0px;

        img.trigger {
            width: 40px;
            height: 30px;
        }
    }
}

.createAccount {
    width: 100%;
    margin: 0 auto;
    padding: 1rem;
    border-radius: 8px;
    border: 4px solid #47A8D4;
    box-shadow: 2px 0 6px rgba(0,0,0,.5);
    label {
        margin-top: 0.25em;
        display: block;
    }
    input {
        width: 100%;
        display: block;
        border: 1px solid #efefef;
        padding: .4rem;
    }
}

.cbtn-primary > i {
    vertical-align: middle;
}
</style>
