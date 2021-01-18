<template>
  <div class="teacher-view">

    <template v-if="teacher_context==='student'">
        <TeacherGradeSubmission :user="user" :organization="organization" :project="project" :teacher_context="teacher_context" :direct="direct"/>
    </template>

    <template v-else>
      <TeacherAssignTabs :user="user" :organization="organization" :project="project" :teacher_context="teacher_context" :direct="direct" :assignment="assignment"/>
    </template>

  </div>
</template>

<script>
import TeacherAssignTabs from '../components/CanvasTeacherAssignTabs.vue'
import TeacherGradeSubmission from '../components/CanvasTeacherGradeSubmission.vue'
export default {
  name: 'TeacherWrapper',
  components: {
    TeacherAssignTabs,
    TeacherGradeSubmission
  },
  props: ['user','organization','project','teacher_context', 'direct', 'assignment']
}
</script>

<style>

</style>
