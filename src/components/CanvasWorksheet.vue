<template>
<div>
  <a v-if="project.worksheet_pdf && !is_reflections" :href="project.worksheet_pdf" download target="_blank" class="print"><img src="../assets/img/canvas/print.svg" alt="print icon" /> printable worksheet</a>
  <form class="canvas-custom-form frame p-base m-center" :class="{'is_submission':is_submission}">
    <div class="customFormQuestion" v-for="(item,i) in worksheet" :key="'q' + i">

      <template v-if="item.type === 'heading'">
        <h3 v-if="item.prompt === 'Reflection Questions'" class="fs-b2 serif color-b">{{item.prompt}}</h3>
        <h3 v-else class="fs-b1">{{item.prompt}}</h3>
      </template>
      <label v-else class="label">{{item.prompt}}</label>

      <!-- ******* TEXTFIELDS ************ -->
      <div v-if="item.type === 'short-text' || item.fieldType==='text' ">
        <template v-if="!is_submission">
          <el-input v-model="answers[i]" :placeholder="item.placeholder" />
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <!-- ******* TEXTAREA ************ -->
      <div v-else-if="item.type === 'long-text' || item.fieldType === 'p' || item.fieldType === 'textarea'">
        <template v-if="!is_submission">
          <el-input type="textarea" v-model="answers[i]" />
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <!-- ******* TRUE FALSE ************ -->
      <div v-else-if="item.type==='true-false'">
        <template v-if="!is_submission">
          <el-radio-group v-model="answers[i]">
            <el-radio label="true">true</el-radio>
            <el-radio label="false">false</el-radio>
          </el-radio-group>
        </template>
        <template v-else>
          <el-radio-group v-model="answers[i]">
            <el-radio label="true" disabled>true</el-radio>
            <el-radio label="false" disabled>false</el-radio>
          </el-radio-group>
        </template>
      </div>

      <div v-else-if="item.type==='yes-no'">
        <template v-if="!is_submission">
          <el-radio-group v-model="answers[i]">
            <el-radio label="yes">yes</el-radio>
            <el-radio label="no">no</el-radio>
          </el-radio-group>
        </template>
        <template v-else>
          <el-radio-group v-model="answers[i]">
            <el-radio label="yes" disabled>yes</el-radio>
            <el-radio label="no" disabled>no</el-radio>
          </el-radio-group>
        </template>
      </div>

      <!-- ******* SELECT ONE ************ -->
      <div v-else-if="item.type==='select-one'">
        <template v-if="!is_submission">
          <el-select v-model="answers[i]" placeholder="Select an answer">
            <el-option v-for="o in item.options" :key="o" :label="o" :value="o"></el-option>
          </el-select>
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>


      <!-- ******* Date ************ -->
      <div v-else-if="item.type === 'date' || item.fieldType === 'date'">
        <template v-if="!is_submission">
          <el-date-picker v-model="answers[i]" type="date" placeholder="Pick a day" />
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <div v-else-if="item.type === 'time' || item.fieldType === 'time'">
        <template v-if="!is_submission">
          <el-time-picker v-model="answers[i]" placeholder="Pick the time"></el-time-picker>
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <div v-else-if="item.type === 'datetime' || item.fieldType === 'datetime'">
        <template v-if="!is_submission">
          <el-date-picker v-model="answers[i]" type="datetime" placeholder="Pick a day and time" />
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <!-- ******* Number ************ -->
      <div v-else-if="item.type === 'number' || item.fieldType === 'number'">
        <template v-if="!is_submission">
          <el-input-number v-model="answers[i]" />
        </template>
        <template v-else>
          <p>{{answers[i]}}</p>
        </template>
      </div>

      <!-- ******* ADD PHOTO ************ -->
      <div v-else-if="item.type === 'file' || item.fieldType ==='file' || item.type === 'picture' || item.fieldType === 'picture'">
        <template v-if="!is_submission">
          <el-upload
            drag
            with-credentials
            action="/core/upload"
            :on-success="function(resp) { answers[i] = resp.url}"
            :file-list="fileList">
            <div class="el-upload__text">Drop file here or <em>click to upload</em></div>
          </el-upload>
        </template>
        <template v-else>
          <img :src="answers[i]" class="answer-img" />
        </template>
      </div>

      <!-- ***** CUSTOM FORM RADIOS ***********-->
      <div v-else-if="item.fieldType==='radio'">
        <el-radio-group v-model="answers[i]">
          <el-radio v-for="o in item.options" :label="o" :key="o + i">{{o}}</el-radio>
        </el-radio-group>
      </div>

      <div v-else-if="item.type == 'rating' || item.fieldType==='rating'">
        <el-radio-group v-model="answers[i]">
          <el-radio :key="label" v-for="label in _.split(field.extra, ',').map(_.trim)" :label="label">{{ label }}</el-radio>
        </el-radio-group>
      </div>

      <!-- ******* CUSTOM FORM CHECKBOXES & SELECT MANY ************ -->
      <div v-else-if="item.fieldType === 'checkbox' || item.type==='select-many'">
        <template v-if="!is_submission">
          <el-checkbox-group v-model="answers[i]">
            <el-checkbox v-for="c in item.options" :key="c + i" :label="c"></el-checkbox>
          </el-checkbox-group>
        </template>
        <template v-else>
          <el-checkbox-group v-model="answers[i]">
            <el-checkbox v-for="c in item.options" :key="c + i" :label="c" disabled></el-checkbox>
          </el-checkbox-group>
        </template>
      </div>


    </div>
    <template v-if="!is_submission">
      <button @click="submitWorksheet" type="button" class="cbtn-primary cbtn-large"><i ref="submit_spinner"></i>Submit</button>
    </template>

  </form>

</div>
</template>

<script>
export default {
    name: 'Worksheet',
    props: ['worksheet','is_submission','project', 'user', 'is_reflections'],
    data: function(){
        return {
            longitude: null,
            latitude: null,
            answers: [],
            fileList: []
        }
    },
    computed: {
        where() {
            if(this.longitude === null || this.latitude === null) {
                return null;
            }

            return [this.longitude, this.latitude];
        },

        questions() {
            return this.worksheet.map(item => item.prompt);
        },

        answered() {
            var ret = {"where": this.where};

            for(var i = 0; i < this.questions.length; i++) {
                ret[this.questions[i]] = this.answers[i];
            }

            return ret;
        },

        xcsrftoken() {
            return JSON.parse(document.getElementById('data-xcsrf-token').textContent);
        }
    },
    methods: {
        createWorksheetOptions(){
            if (!this.is_submission) {
                this.worksheet.map(function(d){
                    if (d.type==='select-one' && d.extra !== ''){
                        d.options = d.extra.split(',')
                    }
                    return d;
                })
            }
        },

        buildAnswers(){
            let ctx = this
            this.worksheet.forEach(function(d){
                // if this is displaying a student submission
                if (ctx.is_submission) {
                    ctx.answers.push(d.answer)
                } else if (d.name === 'Select Many' || d.name == 'selectMany') {
                    ctx.answers.push([]);
                } else {
                    ctx.answers.push('');
                }

            })
        },

        submitWorksheet(){
            var ctx = this;

            ctx.$refs.submit_spinner.classList.add("spinner-border", "spinner-border-sm");

            fetch(ctx.is_reflections ? ctx.user.submit_reflection : ctx.user.submit_worksheet, {
                method: "POST",
                credentials: "include",
                headers: {"X-XCSRFToken": ctx.xcsrftoken},
                body: JSON.stringify(ctx.answered)
            }).then(response => response.text()).then(function() {
                ctx.$refs.submit_spinner.classList.remove("spinner-border", "spinner-border-sm");
                ctx.$emit('worksheetCompleted');
                alert("Your answers have been saved.");
            });
        },

        printWorksheet(){

        }

    },

    mounted() {
        console.log(this.worksheet);
        var ctx = this;
        if(navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(function(loc) {
                ctx.longitude = loc.coords.longitude;
                ctx.latitude = loc.coords.latitude;
            });
        }
    },

    created() {
        this.createWorksheetOptions()
        this.buildAnswers()
    }
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasCustomForm.scss';
.cbtn-primary > i {
    vertical-align: middle;
}
</style>
