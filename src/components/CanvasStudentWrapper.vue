<template>
  <div class="student-assign student content-wrapper">

      <StudentAssignmentTabs :completed="completed" :project="project" :user="user" :direct="direct" :organization="organization" :assignment_settings="assignment_settings" @worksheetCompleted="showThankYou" />

  </div>
</template>

<script>
import StudentAssignmentTabs from '../components/CanvasStudentAssignTabs'
export default {
  name: 'StudentWrapper',
  components: {
    StudentAssignmentTabs
  },
  props: ['project','user','organization','assignment_settings', 'direct'],
  data: function() {
    return {
      completed: !!this.user.assignment_done
    }
  },
  methods: {
    showThankYou() {
      this.completed = true
    }
  }
}
</script>

<style>

</style>
