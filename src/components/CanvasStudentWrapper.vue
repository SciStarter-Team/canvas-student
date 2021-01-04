<template>
  <div class="student-assign student content-wrapper">

      <StudentCompletedProject v-if="completed" :project="project" :user="user" :organization="organization" :direct="direct"/>
      <StudentAssignmentTabs v-else :project="project" :user="user" :organization="organization" :assignment_settings="assignment_settings" @worksheetCompleted="showThankYou" :direct="direct"/>

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
  props: ['project','user','organization','assignment_settings', 'direct'],
  data: function(){
    return {
      completed: !!this.user.assignment_done
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
