<template>
<div>

  <template v-if="!is_submission">
    <div v-if="project.project.type==='Project'" class="flex flex-jc-sb width-80 m-center" style="margin-bottom:2rem;margin-top:1rem;">
      <h3 class="color-p fs-b2 serif w-700 m-0-0-s4">Project Worksheet</h3>
      <a @click="printWorksheet" class="print"><img src="../assets/img/canvas/print.svg" alt="print icon" /> print worksheet</a>
    </div>
    <div v-else class="width-80 m-center" style="margin-bottom:2rem;margin-top:1rem;">
      <h3 class="color-p fs-b2 serif w-700 m-0-0-s4">Project Worksheet</h3>
    </div>
  </template>

  <form class="canvas-custom-form frame width-80 p-base m-center" :class="{'is_submission':is_submission}" @submit="submitWorksheet">



    <div class="customFormQuestion" v-for="(item,i) in worksheet" :key="'q' + i">

        <template v-if="item.type === 'heading'">
          <h3 v-if="item.prompt === 'Reflection Questions'" class="fs-b2 serif color-b">{{item.prompt}}</h3>
          <h3 v-else class="fs-b1">{{item.prompt}}</h3>
        </template>

        <label v-else class="label">{{item.prompt}}</label>


        <!-- ******* TEXTFIELDS ************ -->
        <div v-if="item.type === 'short-text' || item.fieldType==='textField' ">
          <template v-if="!is_submission">
            <el-input v-model="answers[i]" :placeholder="item.placeholder" />
          </template>
          <template v-else>
            <p>{{answers[i]}}</p>
          </template>
        </div>

        <!-- ******* TEXTAREA ************ -->
        <div v-else-if="item.type === 'long-text' || item.fieldType === 'p' || item.fieldType === 'paragraphField'">
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
              <el-radio label="true">yes</el-radio>
              <el-radio label= "false">no</el-radio>
            </el-radio-group>
          </template>
          <template v-else>
            <el-radio-group v-model="answers[i]">
              <el-radio label="true" disabled>yes</el-radio>
              <el-radio label= "false" disabled>no</el-radio>
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
        <!-- ******* YOU'LL HAVE TO FIGURE THIS OUT FOR YOUR NEEDS, it might need video ************ -->
        <div v-else-if="item.type === 'file' || item.fieldType==='file'">
          <template v-if="!is_submission">
            <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :on-preview="handlePreview"
              :on-remove="handleRemove"
              :before-remove="beforeRemove"
              multiple
              :limit="3"
              :on-exceed="handleExceed"
              :file-list="fileList">
              <el-button size="small" type="primary">Click to upload</el-button>
              <div slot="tip" class="el-upload__tip">jpg/png files with a size less than 500kb</div>
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

        <!-- ******* CUSTOM FORM CHECKBOXES & SELECT MANY ************ -->
        <div v-else-if="item.fieldType === 'checkbox' || item.type==='select-many'">
          <template v-if="!is_submission">
            <el-checkbox-group v-model="answers[i]">
              <el-checkbox v-for="c in item.options" :key="c" :label="c"></el-checkbox>
            </el-checkbox-group>
          </template>
          <template v-else>
            <el-checkbox-group v-model="answers[i]">
              <el-checkbox v-for="c in item.options" :key="c" :label="c" disabled></el-checkbox>
            </el-checkbox-group>
          </template>
        </div>




    </div>
    <template v-if="!is_submission">
    <button class="cbtn-primary cbtn-large">Submit</button>
  </template>

  </form>

</div>
</template>

<script>
export default {
  name: 'Worksheet',
  props: ['worksheet','is_submission','project'],
  data: function(){
    return {
      answers:[]
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
        } else {
          ctx.answers.push('')
        }

      })
    },
    submitWorksheet(){
      // does this need logic for if there is more than one contribution required?
      // or is there only one reflection?
      this.$emit('worksheetCompleted') // this might be able to be removed

    },
    printWorksheet(){

    }

  },
  created(){
    this.createWorksheetOptions()
    this.buildAnswers()
  }

}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';
@import '../assets/css/CanvasCustomForm.scss';
</style>
