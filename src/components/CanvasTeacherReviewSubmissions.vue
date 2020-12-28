<template>
  <div class="teacher-review-submissions p-base">
    <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Student Submissions</h3>
    <p class="m-0-0-lg">Individual submissions may be reviewed within speedgrader.</p>
    <a @click="downloadSubmissions" class="cbtn-primary">Download {{assignment_submissions.length}} Submission<template v-if="assignment_submissions.length > 1">s</template> as a CSV</a>

  </div>
</template>

<script>
/**** REMOVE FOR PROD *********/
import store from '../store.js'
/*******************************/

export default {
  name: 'TeacherReviewSubmissions',
  data: function(){
    return {
      assignment_submissions: [
        store.assignment_submission,
        store.assignment_submission,
        store.assignment_submission
      ]
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
