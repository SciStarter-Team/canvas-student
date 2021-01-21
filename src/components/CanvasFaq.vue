<script>
import VueMarkdown from 'vue-markdown'

export default {
    props: ['slug'],

    data: function() { return {
        override: false,
        copy: '',

        selected: [],
        faq: {},
    }},

    components: {
        VueMarkdown
    },

    computed: {
        all_tags: function() {
            var ret = [];

            if(!this.faq.qandas)
                return ret;

            for(var i = 0; i < this.faq.qandas.length; i++) {
                for(var j = 0; j < this.faq.qandas[i].tags; j++) {
                    if(ret.indexOf(this.faq.qandas[i].tags[j] < 0)) {
                        ret.push(this.faq.qandas[i].tags[j]);
                    }
                }
            }

            return ret;
        },
    },

    mounted: function() {
        var ctx = this;

        fetch('https://scistarter.org/canvas/api/faq?slug=' + ctx.slug, {
            method: "GET",
        }).then(response => response.json()).then(function(data) {
            ctx.faq = data;
        });
    },

    methods: {
        intersects: function(a, b) {
            return a.filter(function(n) {
                return b.indexOf(n) !== -1;
            }).length > 0;
        }
    }
}
</script>

<template>
<div class="ss-faq ss-org-teacher-faq ss-org-style">
  <div class="project-action wrap org-section-bg-gray">
    <div class="org-wrap-header u-mb u-p">
      <h1>{{ faq.name }}</h1>
      <div>{{ faq.intro || '' }}</div>
    </div>
    <el-select v-if="all_tags.length" v-model="selected" multiple placeholder="Answers tagged...">
      <el-option v-for="tag in all_tags" :key="tag" :label="tag" :value="tag"></el-option>
    </el-select>

    <div class="faq-container u-pl u-pr u-pb">
      <el-collapse>
        <template v-for="qanda in faq.qandas">
          <el-collapse-item
            v-if="selected.length == 0 || intersects(qanda.tags, selected)"
            class="u-mb"
            :key="qanda.question"
            >
            <template slot="title">
              <span class="">{{ qanda.question }}</span>
            </template>
            <vue-markdown :source="qanda.answer"/>
          </el-collapse-item>
        </template>
      </el-collapse>
    </div>
  </div>
</div>
</template>

<style lang="scss">
  .ss-faq {
    h1 {
      font-size: 1.5em;
    }

    .el-collapse-item__header {
        background-color: #ddd;
        cursor: pointer;
        color: #47a8d4;
        padding: 1em;
        border-top: 1px solid #aaa;

        span {
            text-decoration: underline;
            line-height: normal;
            word-break: break-word;
        }
    }

    .el-collapse-item__content {
        padding: 1em;

        div {
            word-break: break-word;
        }
    }
  }
</style>
