<template>
<div class="teacher-review-submissions p-base">
  <template v-if="assignment_submissions.available">
    <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Student Submissions</h3>
    <a download :href="assignment_submissions.download" class="cbtn-primary">Download {{assignment_submissions.length}} Submission<template v-if="assignment_submissions.length != 1">s</template> as a Spreadsheet</a>
    <hr>
  </template>

  <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Record Data</h3>
  <p>
    As a teacher, you'll enter data directly into the project. To do
    that now, visit the project's web site:
  </p>
  <p class="fs-b1 m-base-0-b4">
    <a class="cbtn-primary" target="_blank" :href="project.project.url"><b>{{project.project.name}} website &raquo;</b></a>
  </p>

  </div>
</template>

<script>
export default {
    name: 'TeacherReviewSubmissions',
    props: ["user", "organization", "project"],
    data: function() {
        return {

        };
    },

    computed: {
        assignment_submissions() {
            let el = document.getElementById('data-submissions');
            return JSON.parse(el ? el.textContent : '{"available": false}');
        },
    },
}
</script>

<style lang="scss">

</style>
