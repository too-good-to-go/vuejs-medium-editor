<template>
  <div>
    <!-- Editor Mode -->
    <div class="medium-editor-container" v-if="!readOnly">
      <insert-embed v-if="editor"
                    :uploadUrl="options.uploadUrl"
                    :uploadUrlHeader="options.uploadUrlHeader"
                    :file_input_name="options.file_input_name"
                    :imgur_bool="options.imgur"
                    :onChange="triggerChange"
                    :editorRef="$refs.editor"
                    :editor="editor"
                    v-on:uploaded="uploadedCallback"></insert-embed>
      <list-handler v-if="editor"
                    :editor="editor"
                    :onChange="triggerChange"></list-handler>
      <div class="editor"
           v-bind:class="editorClass"
           v-html="prefill"
           ref="editor">
      </div>
    </div>
    <!-- Read Only Mode -->
    <read-mode v-if="readOnly" :content="prefill"></read-mode>
  </div>
</template>

<script>
import MediumEditor from 'medium-editor';
import InsertEmbed from './libs/InsertEmbed';
import ListHandler from './libs/ListHandler';
import ReadMode from './libs/ReadMode';
import _ from 'underscore';

export default {
  name: "medium-editor",
  data() {
    return {
      editor: null,
      defaultOptions: {
        forcePlainText: false,
        placeholder: {
          text: "Write something great!!"
        },
        uploadUrl: "https://api.imgur.com/3/image",
        uploadUrlHeader: {'Authorization': 'Client-ID db856b43cc7f441'},
        file_input_name: "image",
        imgur: true,
        toolbar: {
          buttons: ["bold", "italic", "h2", "h3",]
        }
      },
      hasContent: false,
      autoLink: true
    };
  },
  props: ["options", "onChange", "prefill", "readOnly"],
  computed: {
    editorOptions() {
      return _.extend(this.defaultOptions, this.options);
    },
    editorClass() {
      return {
        'has-content': this.hasContent
      }
    }
  },
  components: {
    InsertEmbed,
    ListHandler,
    ReadMode
  },
  mounted() {
    this.addClassToH2();
    if (!this.readOnly) {
      this.createElm();
    }
  },
  methods: {
    createElm() {
      this.editor = new MediumEditor(this.$refs.editor, this.editorOptions);
      if (this.prefill) {
        if (/<[a-z][\s\S]*>/i.test(this.prefill)) {
          this.hasContent = true;
        } else {
          this.hasContent = false;
        }
        this.$refs.editor.focus();
      }
      this.editor.subscribe("editableInput", this.triggerChange);
    },
    destroyElm() {
      this.editor.destroy();
    },
    triggerChange() {
      this.addClassToH2() ;
      const content = this.editor.getContent();
      setTimeout(() => {
        if (/<[a-z][\s\S]*>/i.test(content)) {
          this.hasContent = true;
        } else {
          this.hasContent = false;
        }
      }, 0);
      this.$emit("input", content);
      if (this.onChange) {
        this.onChange(content);
      }
    },
    uploadedCallback(url) {
      this.$emit("uploaded", url);
    },
    addClassToH2() {
      let h2Class = 'h2 h2-small';
      let h3Class = 'h3 h3-small';

      document.querySelectorAll('h2').forEach((block) => {
        block.classList = h2Class;
        console.log('h2');
      });
      document.querySelectorAll('h3').forEach((block) => {
        block.classList = h3Class;
        console.log('h3');

      });
    }
  },
  destroyed() {
    this.destroyElm();
  }
};
</script>
