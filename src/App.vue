<template>
    <div id="app">
        <tgtg-editor
            v-model="content"
            :prefill="defaultValue"
            :options="options"
            @uploaded="uploadCallback">
        </tgtg-editor>
<!--      :onChange="onChange"-->
    </div>
</template>

<script>
import Editor from "./Editor.vue";
export default {
  name: "app",
  data() {
    return {
      content: ``,
        defaultValue: `<h1>Vuejs <b>Medium Editor</b></h1>
          <div class="editor-media is-full"><img src="https://source.unsplash.com/yxNURc8he3o/2000x600"></div>
   <p>At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus</p>
          <div class="editor-media is-normal"><img src="https://source.unsplash.com/DKnXlH_r3x4/2000x800"></div>
          <div class="editor-media-description">you how all this mistaken idea of denouncing pleasure</div>
          <input class="editor-btn" type="button" :link="https://google.com" :newTab="_self" onclick="location.href='https://google.com';" value="Go to Google"/>
          <div class="editor-media">
          <iframe src="https://www.youtube.com/embed/g3p3nMiwl7M" frameborder="0" allowfullscreen></iframe>
          </div>
          <table>
          <thead>

</thead>
<tbody>
nono
<tr><td>
no1
</td>
<td>34</td></tr>
<tr><td>
no2
</td>
<td>36</td></tr>
</tbody>
</table>
          <p>On the other hand, we denounce with righteous indignation and dislike men who are so beguiled and demoralized by the charms of pleasure of the moment, so blinded by desire, that they cannot foresee the pain and trouble that are bound to ensue; and equal blame belongs to those who fail in their duty through weakness of wills</p>`,
      options: {
        uploadUrl: "https://api.imgur.com/3/image",
        uploadUrlHeader: {'Authorization': 'Client-ID db856b43cc7f441'},
        file_input_name: "image",
        imgur: true,
        toolbar: {
          buttons: ["bold", "italic",
          {
              name: 'anchor',
              action: 'createLink',
              aria: 'link',
              tagNames: ['a'],
              contentDefault: '<b>🔗</b>',
              contentFA: '<i class="fa fa-link"></i>',
          },
           "quote",  "h2", "h3",
          'unorderedlist', 'orderedlist',]
        }
      }
    };
  },
  components: {
    "tgtg-editor": Editor
  },
  methods: {
    uploadCallback() {
      //console.log('uploaded');
    }
  },
  mounted(){

  }
};
</script>

<style lang="css">
@import "~bulma/css/bulma.css";
@import "~medium-editor/dist/css/medium-editor.css";
@import "./themes/default.scss";
/*@import '~highlight.js/styles/github.css';*/
/*@import '~highlight.js/styles/ocean.css';*/
@import '~highlight.js/styles/androidstudio.css';
</style>
