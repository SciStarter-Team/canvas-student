<template>
<div class="teacher-review-submissions p-base">
  <template v-if="assignment_submissions.available">
    <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Student Submissions</h3>
    <a download :href="assignment_submissions.download" class="cbtn-primary">Download {{assignment_submissions.length}} Submission<template v-if="assignment_submissions.length != 1">s</template> as a Spreadsheet</a>
    <hr>
  </template>

  <template v-if="assignment_submissions.reflections_available">
    <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Student Reflections</h3>
    <a download :href="assignment_submissions.reflections_download" class="cbtn-primary">Download {{assignment_submissions.reflections_length}} Reflection<template v-if="assignment_submissions.reflections_length != 1">s</template> as a Spreadsheet</a>
    <hr>
  </template>

  <h3 class="color-b fs-b4 serif w-700 m-0-0-s4">Record Data</h3>
  <template v-if="project.project.type==='CustomProject'">
    <Worksheet :user="user" :worksheet="worksheet" :project="project" v-on="$listeners" />
  </template>
  <template v-else>
    <p>
      As a teacher, you can enter data directly into the project. To do
      that now, visit the project's web site:
    </p>
    <p class="fs-b1 m-base-0-b4">
      <a class="cbtn-primary" target="_blank" :href="project.project.url"><b>{{project.project.name}} website &raquo;</b></a>
    </p>
  </template>
</div>
</template>

<script>
import Worksheet from '../components/CanvasWorksheet.vue'
export default {
    name: 'TeacherReviewSubmissions',
    components: {
        Worksheet
    },
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

        worksheet() {
            if (this.project.project.type == 'CustomProject') {
                var data = this.project.project.json
                if(typeof(data) === "string") {
                    data = JSON.parse(data);
                }
                return data;
            } else {
                return this.project.worksheet ? this.project.worksheet.fields : null;
            }
        }
    },
}
</script>

<style lang="scss">

</style>
