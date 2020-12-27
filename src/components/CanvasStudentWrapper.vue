<template>
  <div class="student-assign student content-wrapper">

      <StudentCompletedProject v-if="completed" :project="project" :user="user" :organization="organization" />
      <StudentAssignmentTabs v-else :project="project" :user="user" :organization="organization" @worksheetCompleted="showThankYou" />

  </div>
</template>

<script>
import StudentCompletedProject from '../components/CanvasStudentCompletedProject'
import StudentAssignmentTabs from '../components/CanvasStudentAssignTabs'
export default {
  name: 'StudentWrapper',
  components: {
    StudentCompletedProject,
    StudentAssignmentTabs
  },
  props: ['project','user','organization'],
  data: function(){
    return {
      completed: false // the student has completed the project
    }
  },
  methods: {
    showThankYou: function(){
      this.completed = true
    }
  }
}
</script>

<style>

</style>
