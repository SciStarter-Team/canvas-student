<template>
  <div class="student-assign student content-wrapper">

      <StudentAssignmentTabs :completed="completed" :project="project" :user="user" :direct="direct" :organization="organization" @setCompleted="completed=true" :assignment_settings="assignment_settings" :assignment="assignment" @worksheetCompleted="showThankYou" />

  </div>
</template>

<script>
import StudentAssignmentTabs from '../components/CanvasStudentAssignTabs'
export default {
  name: 'StudentWrapper',
  components: {
    StudentAssignmentTabs
  },
  props: ['project','user','organization','assignment_settings', 'assignment', 'direct'],
  data: function() {
    return {
      completed: !!this.user.assignment_done
    }
  },
  methods: {
    showThankYou() {
        this.completed = true;
        window.location.hash = '#reflections';
    }
  }
}
</script>

<style>

</style>
