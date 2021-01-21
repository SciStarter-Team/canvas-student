<template>
<div class="student-do-project canvas-style">
  <template v-if="!user.via_school && !user.hasAccount">
    <div class="p-base">
      <h2 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h2>
      <p>
        You will need to sign in to a SciStarter account, below,
        before logging data. If you do not have a SciStarter account,
        just enter your email and a password, and a free account will
        be created for you.
      </p>
      <form class="createAccount" @submit.prevent="createAccount">
        <label class="label">Email</label>
        <input type="email" v-model="signup_email"/>
        <label class="label">Password</label>
        <input type="password" v-model="signup_password"/>
        <button type="submit" class="cbtn-primary"><i ref="join_spinner"></i>Sign in to SciStarter</button>
      </form>
    </div>
  </template>
  <template v-else>
    <!-- project hosted on SciStarter e.g. Stream Selfie, Project Squirrel -->
    <template v-if="project.project.form && project.project.type == 'Project'">
      <Worksheet :user="user" :worksheet="worksheet" :project="project" v-on="$listeners" />
    </template>


    <!-- project NOT hosted on SciStarter e.g. Stall Catchers -->
    <template v-else-if="!project.project.form  && project.project.type == 'Project'">

      <!-- Teacher submits data so student does not need to go to project? -->
      <template v-if="assignment_settings.submitted_by === 'teacher'">
        <p>Your teacher will lead you through the project!</p>

        <div class="m-b4-0 p-b2-0" style="border-top:2px solid #9f9f9f">
          <h2 class="color-p fs-base serif w-700 m-0-0-s4">Project Worksheet</h2>
          <Worksheet :user="user" :worksheet="worksheet" :project="project" v-on="$listeners" />
        </div>
      </template><!-- END Teacher submits data so student does not need to go to project? -->



      <!-- The student does not have an account in SciStarter already -->
      <template v-else-if="assignment_settings.submitted_by === 'student' && !user.hasAccount">

        <!-- Before creating account -->
        <template v-if="!accountCreated">
          <div class="p-base">
            <h2 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h2>
            <ol class="instructions">
              <li>You will create a SciStarter account with your school email address, below.</li>
              <li>On the project's website, <b class="w-700">create an account with your same school email address (case sentitive)</b>.</li>
              <li v-if="project.reflections">Come back to this page and fill out the reflection questions.</li>
            </ol>
            <form class="createAccount" @submit.prevent="createAccount">
              <label class="label">School email</label>
              <strong>{{user.email}}</strong>
              <label class="label">Password</label>
              <input type="password" v-model="signup_password"/>
              <button type="submit" class="cbtn-primary"><i ref="join_spinner"></i>Create SciStarter Account</button>
            </form>
          </div>
        </template><!-- END Before creating account -->

        <!-- After creating account -->
        <template v-else>
          <div class="p-base">

            <div>
              <h2 class="color-p fs-b2 serif w-700 m-0-0-base">Go to the Project Website</h2>

              <ol class="instructions">
                <li v-if="assignment.contributions">Your teacher wants you to do the project {{assignment.contributions}} time<template v-if="assignment.contributions > 1">s</template>.
                  <p><i>Remember to create an account with your same school email address if you haven't already (case sensitive).</i></p>
                  <p class="fs-b1 m-base-0-b4"><a class="cbtn-primary" target="_blank" :href="project.project.url"><b>{{project.project.name}} website &raquo;</b></a></p>
                </li>
                <li v-else>Participate in the project.</li>
                <li>Come back to this page and fill out the worksheet below.</li>
                <li v-if="project.reflections">Fill out the reflection questions below.</li>
              </ol>
            </div>

            <div class="m-b4-0 p-b2-0" style="border-top:2px solid #9f9f9f">
              <Worksheet :user="user" :worksheet="worksheet" :project="project" v-on="$listeners" />
            </div>


          </div>
        </template><!-- END After creating account -->


      </template> <!-- END The student does not have an account in SciStarter already -->

      <!-- The student has an account -->
      <div v-else class="p-base">
        <div>
          <h2 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h2>
          <ol class="instructions">
            <li>After you’ve read about the project and reviewed the instructions on this page, click the orange button below to visit the project’s website. This site will open in a new tab.</li>
            <li>On the project’s website, create a project account using your school email address (case sensitive): <strong>{{user.email}}</strong></li>
            <li>Following instructions on the project’s website, you will add observations to help scientists answer questions they can’t answer without you!</li>
            <li v-if="project.reflections">Return to this page and complete the reflection questions, below. Your teacher will be notified when you’ve submitted your reflections.</li>
          </ol>
          <p class="fs-b1 m-base-0-b4"><a class="cbtn-primary" target="_blank" :href="project.project.url"><b>{{project.project.name}} website &raquo;</b></a></p>
        </div>
      </div>


    </template><!-- END project NOT hosted on SciStarter e.g. Stall Catchers -->

    <!-- Custom Project -->
    <template v-else-if="project.project.type == 'CustomProject'">
      <h2 class="color-p fs-base serif w-700 m-0-0-s4">Project Worksheet</h2>
      <Worksheet :user="user" :worksheet="worksheet" :project="project" v-on="$listeners" />
    </template>

    <template v-if="project.reflections">
      <h2 class="color-p fs-base serif w-700 m-0-0-s4">Reflection Questions</h2>
      <p v-if="submitted_reflections">
        You have already submitted your reflection questions. If you'd
        like to revise your answers or resubmit, you may do so below.
      </p>
      <Worksheet @worksheetCompleted="submitted_reflections = true" :user="user" :worksheet="project.reflections.fields" :project="project" :is_reflections="true" />
    </template>
  </template>
</div>
</template>

<script>
import Worksheet from '../components/CanvasWorksheet.vue'
export default {
    name: 'StudentDoProject',
    components: {
        Worksheet
    },
    props: ['project','user','organization','assignment_settings'],
    data: function(){
        return {
            signup_email: "",
            signup_password: "",
            accountCreated: false, // set to true on account creation
            assignment: {
                contributions: 1,
            }, // set assignment stuff
            submitted_reflections: false,
        }
    },
    computed: {
        worksheet() {
            if (this.project.project.type == 'CustomProject') {
                var data = this.project.project.json
                if(typeof(data) === "string") {
                    data = JSON.parse(data);
                }
                return data;
            } else {
                return this.project.worksheet ? this.project.worksheet.fields : null;
            }
        },

        xcsrftoken() {
            return JSON.parse(document.getElementById('data-xcsrf-token').textContent);
        }
    },
    methods: {
        createAccount: function() {
            var ctx = this;

            var email = ctx.signup_email ? ctx.signup_email : ctx.user.email;

            var body = new FormData();
            body.append("email", email);
            body.append("password", this.signup_password);

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
                    ctx.accountCreated = true;

                    if(data.warning) {
                        alert(data.warning);
                    }
                }
                else {
                    alert(data.error);
                }
            });
        }
    }
}
</script>

<style lang="scss" scoped>
.createAccount {
    width: 50%;
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
