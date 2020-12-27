<template>
  <form class="canvas-custom-form frame width-80 p-base m-center" @submit="submitWorksheet">
    <h2 class="color-p fs-b2 serif w-700 m-0-0-s4 width-80 m-center" style="margin-bottom:2rem;">Worksheet</h2>
    <div class="customFormQuestion" v-for="(item,i) in worksheet" :key="'q' + i">

        <template v-if="item.type === 'heading'">
          <h3 v-if="item.prompt === 'Reflection Questions'" class="fs-b2 serif color-b">{{item.prompt}}</h3>
          <h3 v-else class="fs-b1">{{item.prompt}}</h3>
        </template>

        <label v-else class="label">{{item.prompt}}</label>


        <!-- ******* TEXTFIELDS ************ -->
        <div v-if="item.type === 'short-text' || item.fieldType==='textField' ">
          <el-input v-model="answers[i]" :placeholder="item.placeholder" />
        </div>

        <!-- ******* TEXTAREA ************ -->
        <div v-else-if="item.type === 'long-text' || item.fieldType === 'p' || item.fieldType === 'paragraphField'">
          <el-input type="textarea" v-model="answers[i]" />
        </div>

        <!-- ******* TRUE FALSE ************ -->
        <div v-else-if="item.type==='true-false'">
          <el-radio-group v-model="answers[i]">
            <el-radio label="true">yes</el-radio>
            <el-radio label= "false">no</el-radio>
          </el-radio-group>
        </div>

        <!-- ******* SELECT ONE ************ -->
        <div v-else-if="item.type==='select-one'">
          <el-select v-model="answers[i]" placeholder="Select an answer">
            <el-option v-for="o in item.options" :key="o" :label="o" :value="o"></el-option>
          </el-select>
        </div>


        <!-- ******* Date ************ -->
        <div v-else-if="item.type === 'date' || item.fieldType === 'date'">
          <el-date-picker v-model="answers[i]" type="date" placeholder="Pick a day" />
        </div>

        <!-- ******* Number ************ -->
        <div v-else-if="item.type === 'number' || item.fieldType === 'number'">
          <el-input-number v-model="answers[i]" />
        </div>

        <!-- ******* ADD PHOTO ************ -->
        <!-- ******* YOU'LL HAVE TO FIGURE THIS OUT FOR YOUR NEEDS ************ -->
        <div v-else-if="item.type === 'file' || item.fieldType==='file'">
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
        </div>

        <!-- ***** CUSTOM FORM RADIOS ***********-->
        <div v-else-if="item.fieldType==='radio'">
          <el-radio-group v-model="answers[i]">
            <el-radio v-for="o in item.options" :label="o" :key="o + i">{{o}}</el-radio>
          </el-radio-group>
        </div>

        <!-- ******* CUSTOM FORM CHECKBOXES ************ -->
        <div v-else-if="item.fieldType === 'checkbox'">
            <el-checkbox-group v-model="answers[i]">
              <el-checkbox v-for="c in item.options" :key="c" :label="c"></el-checkbox>
            <!-- <label class="d-block" v-for="c in item.options" :key="c"><input type="checkbox" /> {{c}}</label> -->
            </el-checkbox-group>
        </div>




    </div>
    <button class="cbtn-primary cbtn-large">Submit</button>

  </form>
</template>

<script>
export default {
  name: 'Worksheet',
  props: ['worksheet'],
  data: function(){
    return {
      answers:[]
    }
  },
  methods: {
    createWorksheetOptions(){
      this.worksheet.map(function(d){
        if (d.type==='select-one' && d.extra !== ''){
          d.options = d.extra.split(',')
        }
        return d;
      })
    },
    buildAnswers(){
      let ctx = this
      this.worksheet.forEach(function(){
        ctx.answers.push('')
      })
    },
    submitWorksheet(){
      // does this need logic for if there is more than one contribution required?
      // or is there only one reflection?
      this.$emit('worksheetCompleted') // this might be able to be removed

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
