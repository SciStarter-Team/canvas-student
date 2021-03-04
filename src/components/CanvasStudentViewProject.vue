<template>
<div class="canvas-project-details">
  <template>

    <div id="project-details">

      <div class="flex">
        <div class="flex flex-center-both project-name">
          <div>
            <h1 class="serif fs-b1 color-w ta-center">{{project.project.name}}</h1>
            <template v-if="project.project.type == 'CustomProject'">
              <p class="color-w m-0 ta-center">Shared by {{project.project.teacher.name}}</p>
              <p class="color-w m-0 ta-center">{{project.project.teacher.school}}</p>
            </template>
          </div>
        </div>

        <template v-if="project.project.type == 'Project'">
          <div class="project-video">
            <div class="videoWrapper">
              <video controls class="videoWrapper__video" :poster="intro_poster" preload="auto">
                <template v-for="vid in intro_videos">
                  <source :key="vid.url" :type="vid.type" :src="vid.url">
                </template>
              </video>
            </div>
          </div>
        </template>

        <template v-if="project.project.type == 'CustomProject'">
          <div class="project-video">
            <div class="videoWrapper">
              <img :src="project.project.image" v-if="project.project.image_or_video=='image' && project.project.image">
              <video :src="project.project.video" v-else-if="project.project.image_or_video='video' && project.project.video" controls></video>
              <img src="../assets/img/canvas/robot-background-4_3.jpg" v-else>
            </div>
          </div>
        </template>

      </div>


      <div class="flex flex-jc-sb flex-reverse project-details">

        <div class="flex-col">
          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Description</h3>
            <vue-markdown :source="project.description || project.project.description"></vue-markdown>
          </div>

          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Details</h3>
            <table class="canvas-table">
              <tr v-if="project.project.outdoors || project.project.indoors || project.project.location_type == 'ON'">
                <th scope="row">Spend the time</th>
                <td v-if="project.project.location_type == 'ON'">Online</td>
                <td v-else-if="project.project.outdoors && project.project.indoors">Indoors and Outdoors</td>
                <td v-else-if="project.project.outdoors">Outdoors</td>
                <td v-else>Indoors</td>
              </tr>
              <tr v-if="project.project.topics.length > 0">
                <th scope="row">Topics</th>
                <td>{{topics}}</td>
              </tr>
              <tr v-if="project.project.activities.length > 0">
                <th scope="row">Type of Activity</th>
                <td>{{activities}}</td>
              </tr>
              <tr v-if="project.classroom_materials_link">
                <th scope="row">Classroom materials</th>
                <td><a :href="project.classroom_materials_link" target="_blank">{{ project.classroom_materials_label || project.classroom_materials_link }}</a></td>
              </tr>
              <tr v-else-if="project.project.classroom_materials">
                <th scope="row">Classroom materials</th>
                <td><a :href="project.project.classroom_materials" target="_blank">{{ project.project.classroom_materials }}</a></td>
              </tr>
            </table>
          </div>

          <div v-if="project.student_resources && project.student_resources.length > 0" class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Additional Resources</h3>
            <ul class="standard">
              <li v-for="res in project.student_resources" :key="res.url"><a :href="res.url" target="_blank">{{res.label || res.url}}</a></li>
            </ul>
          </div>
        </div><!-- end .flex-col -->

        <div class="flex-col">

          <div class="frame p-base div1">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Time Required</h3>
            <p class="serif fs-b1 color-b">{{project.time_required || project.project.time }}</p>
          </div>

          <div class="frame p-base div2">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Materials Needed</h3>

            <div class="content-slot" v-html="project.materials"></div>
          </div>
        </div><!-- end .flex-col -->


      </div><!-- end .grid -->

      <div class="frame p-base m-0-basehalf">
        <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Instructions</h3>

        <ol class="instructions">
          <template v-for="(step, idx) in project.steps">
            <li v-if="check_condition(step.condition)" v-html="step.text" :key="idx"></li>
          </template>
        </ol>

      </div>

      <p style="margin-top: 1rem">
        <a @click="triggerDoProjectTab" class="cbtn-primary"><span v-if="assignment_settings.input_mode=='worksheet'">Go to Worksheet</span><span v-else>Do the Project</span></a>
      </p>

    </div><!-- end #project-details -->

  </template>




</div>
</template>

<script>
import VueMarkdown from 'vue-markdown'
export default {
    name: 'StudentViewProject',
    props: ['project', 'user', 'assignment_settings', 'direct', 'organization'],
    data: function(){
        return {
            questions: null
        }
    },

    components: {
        VueMarkdown
    },

    methods: {
        check_condition(condition) {
            return ((condition & this.satisfies) == condition);
        },

        scrollToTop(){
            window.scrollTo(0,0)
        },
        triggerDoProjectTab(){
            this.$emit('triggerTab',2);
        }

    },

    computed: {
        satisfies() {
            var ret = 0x01;

            if(this.direct) {
                ret += 0x10;
            }
            else {
                ret += 0x08;
            }

            return ret;
        },

        intro_videos() {
            return this.project.videos.filter(vid => vid.role === 0);
        },

        intro_poster() {
            // return this.intro_videos.find(vid => !!vid.poster).poster || '';
            var matches = this.intro_videos.filter(function(v) { return !!v.poster});
            if(matches.length >= 1) {
                return matches[0].poster;
            }
            else {
                return '';
            }
        },

        topics(){
            let topics = []
            this.project.project.topics.forEach(d => {
                topics.push(d.label)
            })
            return topics.join(', ')
        },
        activities(){
            let act = []
            this.project.project.activities.forEach(d => {
                act.push(d.label)
            })
            return act.join(', ')
        }

    },

    mounted(){
        this.scrollToTop()
    }
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasProject.scss';
.content-slot {
    margin: 0px;
    border: 0px;
    padding: 0px;

    ul {
        list-style: disc;
        margin-left: 0;
        padding-left: 1.2rem;
    }
}

.canvas-table td {
    overflow-wrap: anywhere;
}
</style>
