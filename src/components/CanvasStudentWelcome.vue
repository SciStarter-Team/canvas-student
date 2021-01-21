<template>
<div class="student-welcome">

  <div class="sw-header m-0-0-base">
    <div class="sw-header-text">
      <h1>Welcome!</h1>
      <p v-if="user.via_school">
        You are using {{organization.display_name}} where you can
        participate in exciting research projects with citizens around
        the globe! The information you collect and share with
        scientists and researchers will help answer important
        questions and better understand our world!
      </p>
      <p v-else>
        A teacher or a student in {{organization.display_name}} has
        created a citizen science project and invited you to
        participate. Learn more about the project's goals and how you
        can get involved in the 'About the Project' tab. Log your data
        contributions in the 'Do the Project' tab. Note: you will need a
        free SciStarter account to log the data. Thank you for helping
        {{organization.display_name}} with this citizen science project.
      </p>
    </div>
  </div>

  <template v-if="user.via_school">
    <h3 class="serif color-p fs-b1 w-700 m-0-0-base">Instructions</h3>
    <ol class="instructions">

      <li><h4 class="fs-base w-700 tt-u color-o">Find out about the project you'll be doing</h4>
        <p>Click on the 'About the Project' tab above to watch an introduction video and read some details about the project your teacher has assigned to you.</p></li>
      <li><h4 class="fs-base w-700 tt-u color-o">Participate in the Project</h4>
        <p>Follow the step-by-step instructions in the 'About the Project' tab above, and answer the questions in the 'Do the Project' tab. Don't forget to submit your answers to the reflection questions, in the same tab.</p></li>
      <li v-if="project.project.type !== 'CustomProject'"><h4 class="fs-base w-700 tt-u color-o">When You're Done, Watch the Thank You Video</h4>
        <p>Once you've finished the project, watch a thank you video from the project and possibly explore some data!</p></li>
    </ol>
  </template>

  <p>
    Before you start on your citizen science adventure, do what
    every good scientist does: research your topic! We've made it
    easy for you. Spend 15 minutes on this self-guided
    "introduction to citizen science" tutorial and review the
    FAQs!
  </p>
  <iframe width="740" height="660" src="https://media.scistarter.org/curated/Broward+Tutorial_January+2021/Broward+Citizen+Science+Tutorial_January_21_2021+-+Storyline+output/story.html"></iframe>
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
