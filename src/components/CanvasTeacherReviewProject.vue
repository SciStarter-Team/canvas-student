<template>
<div class="canvas-project-details">

  <!-- <div class="flex flex-jc-fe m-0-0-base"> -->
    <!--     <a @click="cancelAssignment">cancel assignment</a> -->
    <!-- </div> -->

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

          <!-- DETAILS TABLE -->
          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Details</h3>
            <table class="canvas-table">
              <tr v-if="project.project.question">
                <th scope="row">At a glance</th>
                <td>{{project.project.question}}</td>
              </tr>
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
              <tr v-if="!!project.project.teacher && user.type == 'teacher' && user.id == project.project.teacher.id">
                <th scope="row">Sharing Link</th>
                <td>
                  <p>To invite <strong>non-students</strong> to participate in your project, share this link:</p>
                  <input type="text" :value="project.external_url">
                </td>
              </tr>
            </table>
          </div>

          <!-- DESCRIPTION -->
          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Description</h3>
            <vue-markdown :source="project.description || project.project.description"></vue-markdown>
          </div>

          <!-- RESOURCES FOR TEACHERS -->
          <div v-if="project.teacher_resources && project.teacher_resources.length > 0" class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Additional Resources for Teachers</h3>
            <ul class="standard">
              <li v-for="res in project.teacher_resources" :key="res.url"><a :href="res.url" target="_blank">{{res.label || res.url}}</a></li>
            </ul>
          </div>

          <!-- RESOURCES FOR STUDENTS -->
          <div v-if="project.student_resources && project.student_resources.length > 0" class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Additional Resources for Students</h3>
            <ul class="standard">
              <li v-for="res in project.student_resources" :key="res.url"><a :href="res.url" target="_blank">{{res.label || res.url}}</a></li>
            </ul>
          </div>

        </div><!-- end .flex-col -->

        <div class="flex-col">

          <!-- TIME REQUIRED -->
          <div class="frame p-base div1">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Time Required</h3>
            <p class="serif fs-b1 color-b">{{project.time_required || project.project.time }}</p>
          </div>

          <!-- MATERIALS NEEDED -->
          <div class="frame p-base div2">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Materials Needed</h3>

            <div class="content-slot" v-html="project.materials || project.project.materials"></div>
          </div>

          <!-- IF AFFILIATE WHERE STUDENT HAS TO ENTER DATA -->
          <div v-if="!project.project.form && project.project.type == 'Project'" class="frame p-base message">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Note for Educators</h3>
            <p>
              This project’s data entry form is not hosted on
              SciStarter. If you assign this project and select the
              option for students to log data on their own, they will
              be asked to create a SciStarter account and a project
              account (both are free and both can use the student’s
              school email address). For students ages 12 and under,
              we recommend the assignment option labeled "Students
              will submit data to teacher, Teacher will submit data
              to the project (suggested for younger students)." This
              way, students can <em>practice</em> logging data using
              the worksheets we provide and you have the option to log
              their worksheet data onto the project’s website.
            </p>
          </div>

        </div><!-- end .flex-col -->


      </div><!-- end .flex -->

      <template v-if="project.project.type == 'Project'">
        <div class="frame p-base m-0-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-s4">Student Instructions</h3>

          <ol class="instructions">
            <template v-for="(step, idx) in project.steps">
              <li v-if="check_condition(1, step.condition)" v-html="step.text" :key="idx"></li>
            </template>
          </ol>
        </div>

        <div class="frame p-base m-0-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-s4">Teacher Instructions</h3>

          <ol class="instructions">
            <template v-for="(step, idx) in project.steps">
              <li v-if="check_condition(2, step.condition)" v-html="step.text" :key="idx"></li>
            </template>
          </ol>
        </div>
      </template>
      <template v-else-if="project.project.steps">
        <div class="frame p-base m-0-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-s4">Instructions</h3>

          <vue-markdown :source="project.project.steps"></vue-markdown>
        </div>
      </template>

      <template v-if="project.project.type == 'CustomProject' && project.project.notes">
        <div class="frame p-base m-base-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-base">Notes from the Project Creator</h3>
          <vue-markdown :source="project.project.notes"></vue-markdown>
        </div>
      </template>

      <template v-if="project.project.type == 'CustomProject' && !!worksheet">
        <div class="frame p-base m-base-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-base">Project Questions</h3>
          <CustomForm :fields="worksheet"  />
        </div>
      </template>

      <template v-if="!direct && project.project.type == 'Project' && !!worksheet && worksheet.length > 0">
        <div class="frame p-base m-base-basehalf">
          <div class="flex flex-jc-sb">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Worksheet</h3>
            <a v-if="!!project.worksheet_pdf" :href="project.worksheet_pdf" target="_blank" download class="print"><img src="../assets/img/canvas/print.svg" alt="print icon" /> download printable worksheet</a>
          </div>
          <p class=" m-0-0-lg">This is the form students will fill out and submit to you</p>

          <div v-for="(item,i) in worksheet" class="customFormQuestion" :key="'q'+i">
            <h4 class="label" :class="{'heading':item.type=='heading'}">{{item.prompt}}</h4>
            <small v-if="item.extra">{{item.extra}}</small>

            <input v-if="item.fieldType=='text'" type="text" disabled />
            <textarea v-else-if="item.fieldType=='textarea'" disabled></textarea>

            <el-radio-group v-else-if="item.type==='true-false'">
              <el-radio :value="true" disabled>Yes</el-radio>
              <el-radio :value="false" disabled>No</el-radio>
            </el-radio-group>

            <el-select v-else-if="item.type==='select-one' && item.options" placeholder="Select One" disabled>
              <el-option v-for="o in item.options" :key="o" :value="o" :label="o"></el-option>
            </el-select>

          </div>

        </div>
      </template>

      <template v-if="!!direct || user.type === 'teacher'">
        <!-- participate button here? -->
      </template>

    </div><!-- end #project-details -->

  </template>




</div>
</template>

<script>
import VueMarkdown from 'vue-markdown'
import CustomForm from '../components/CanvasCustomForm'
export default {
    name: 'ViewProject',
    components: {
        VueMarkdown,
        CustomForm
    },
    props: ['project', 'user', 'direct'],
    data: function(){
        return {

        }
    },
    methods: {
        check_condition(constraint, condition) {
            var sat = constraint | this.satisfies;
            return ((condition & sat) == condition);
        },

        scrollToTop() {
            window.scrollTo(0,0)
        },

        createWorksheetOptions() {
            this.worksheet.map(function(d){
                if (d.type==='select-one' && d.extra !== ''){
                    d.options = d.extra.split(',')
                }
                return d;
            })
        },

        printWorksheet(){

        },

        cancelAssignment(){
            let c = confirm("The project will be cancelled. Press OK to remove this project.")
            if (c) {
                // do all the logic to cancel assignment
                // load the other module here??? the one that is usually in the external tool?
                window.location.href = 'http://kevinripka.com/iframe'
            }
        }
    },
    computed: {
        satisfies() {
            var ret = 0;

            if(this.direct) {
                ret |= 0x10;
            }
            else {
                ret |= 0x08;
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

        topics() {
            let topics = []
            this.project.project.topics.forEach(d => {
                topics.push(d.label)
            })
            return topics.join(', ')
        },

        activities() {
            let act = []
            this.project.project.activities.forEach(d => {
                act.push(d.label)
            })
            return act.join(', ')
        },

        worksheet() {
            if (this.project.project.type == 'CustomProject') {
                return this.project.project.json;
            } else {
                return this.project.worksheet ? this.project.worksheet.fields : null;
            }
        }
    },

    mounted() {
        this.scrollToTop();
        this.createWorksheetOptions();
    }
}
</script>

<style lang="scss">
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
