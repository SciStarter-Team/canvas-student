<template>
  <div class="teacher-grade-submission p-base">
    <h3 class="serif color-b fs-b3 w-700 width-80 m-center" style="margin-bottom:1rem">Worksheet Submission</h3>

    <Worksheet :worksheet="answers" :is_submission="true" />

  </div>
</template>

<script>
/**** REMOVE FOR PROD *********/
import store from '../store.js'
/*******************************/

import Worksheet from '../components/CanvasWorksheet.vue'
export default {
  name: 'TeacherGradeSubmission',
  components: {
    Worksheet
  },
  data: function(){
    return {
      assignment_submission:store.assignment_submission,
      answers: store.assignment_submission.answers
    }
  },
  methods: {
    downloadSubmissions(){

    }
  }
}
</script>

<style lang="scss">

</style>
