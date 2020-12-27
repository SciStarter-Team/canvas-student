<template>
  <div class="student-welcome">

    <div class="sw-header m-0-0-base">
        <div class="sw-header-text">
            <h1>Welcome!</h1>
            <p>You are using {{organization.display_name}} where you can participate in exciting research projects with citizens around the globe! The information you collect and share with scientists and researchers will help answer important questions and better understand our world!</p>
        </div>
    </div>

    <h3 class="serif color-p fs-b1 w-700 m-0-0-base">Instructions</h3>
      <ol class="instructions">

      <li><h4 class="fs-base w-700 tt-u color-o">Find out about the project you'll be doing</h4>
      <p>Click on the 'About the Project' tab above to watch an introduction video and read some details about the project you'll be doing.</p></li>
      <li><h4 class="fs-base w-700 tt-u color-o">Participate in the Project</h4>
      <p>You may do the project on your own or with your teacher in the 'Do the Project' tab above.</p></li>
      <li v-if="project.project.type !== 'CustomProject'"><h4 class="fs-base w-700 tt-u color-o">When You're Done, Watch the Thank You Video</h4>
      <p>Once you've finished the project you'll be able to watch a thank you video from the project and possibly explore some data!</p></li>
    </ol>

  </div>
</template>

<script>
export default {
  name: 'StudentWelcome',
  props: ['project','user','organization']
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasStudentWelcome.scss';
</style>
