<template>
  <div class="student-do-project canvas-style">

    <!-- project hosted on SciStarter e.g. Stream Selfie, Project Squirrel -->
    <template v-if="project.project.form && project.project.type == 'Project'">
        <Worksheet :worksheet="worksheet" v-on="$listeners" />
    </template>

    <!-- project NOT hosted on SciStarter e.g. Stall Catchers -->
    <template v-else-if="!project.project.form  && project.project.type == 'Project'">

        <!-- The student does not have an account in SciStarter already -->
        <template v-if="!user.hasAccount">

          <!-- Before creating account -->

          <template v-if="!accountCreated">
            <div class="p-base">
              <h2 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h2>
              <ol class="instructions">
                <li>You will create a SciStarter account with your school email address below.</li>
                <li>Once that is created, you will need to click the link that shows up to the project.</li>
                <li>On that project's website, you will need to <b class="w-700">create an account with your same school email address</b>.</li>
                <li>Participate in the project on their website.</li>
                <li>Come back to this page and fill out the online worksheet.</li>
              </ol>
              <form class="createAccount" @submit.prevent="createAccount()">
                  <label class="label">School email</label>
                  <input type="text" v-model="user.email" />
                  <label class="label">Password</label>
                  <input type="password" />
                  <button type="submit" class="cbtn-primary">submit</button>
              </form>
            </div>
          </template>

          <!-- After creating account -->
          <template v-else>
              <div class="p-base">

                <div>
                  <h2 class="color-p fs-b2 serif w-700 m-0-0-s4">Go to the Project Website</h2>
                  <p class="fs-b1 m-base-0-b4"><a class="cbtn-primary" :href="project.project.url"><b>{{project.project.name}} website &raquo;</b></a></p>
                  <ol class="instructions">
                    <li>Create an account with your same school email addressif you haven't already.</li>
                    <li v-if="assignment.contributions">Your teacher wants you to do the project {{assignment.contributions}} time<template v-if="assignment.contributions > 1">s</template>.</li>
                    <li v-else>Participate in the project.</li>
                    <li>Fill out the worksheet below.</li>
                  </ol>
                </div>

                <div class="m-b4-0 p-b2-0" style="border-top:2px solid #9f9f9f">
                    <Worksheet :worksheet="worksheet" v-on="$listeners" />
                </div>


              </div>
          </template>


        </template>

        <!-- The student has an account -->
        <div v-else class="p-base">
          <div>
            <h2 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h2>
            <ol class="instructions">
              <li>Click the link to the project.</li>
              <li>On that project's website, you will need to <b class="w-700">create an account with your school email address: {{user.email}}</b></li>
              <li>Participate in the project on their website</li>
              <li>Come back to this page and fill out the online worksheet</li>
            </ol>
          </div>
        </div>


    </template>

    <!-- Custom Project -->
    <template v-else-if="project.project.type == 'CustomProject'">
      <Worksheet :worksheet="worksheet" v-on="$listeners" />
    </template>


  </div>
</template>

<script>
import store from '../store.js'

import Worksheet from '../components/CanvasWorksheet.vue'
export default {
  name: 'StudentDoProject',
  components: {
    Worksheet
  },
  props: ['project','user','organization'],
  data: function(){
    return {
      accountCreated: false, // set to true on account creation
      assignment: {
        contributions: 1,
      }, // set assignment stuff
    }
  },
  computed: {
    worksheet: function(){
      if (this.project.project.type == 'CustomProject') {
        return JSON.parse(this.project.project.json)
      } else {
        return store.worksheet
      }
    }
  },
  methods: {
    createAccount: function(){
      this.accountCreated = true
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
  input {
    width: 100%;
    display: block;
    border: 1px solid #efefef;
    padding: .4rem;
  }
}
</style>
