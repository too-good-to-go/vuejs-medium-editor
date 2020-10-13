<template>
  <button class="btn-toggle" v-on:click="addButton()">
    <font-awesome-icon :icon="['fas', 'ad']"/>
  </button>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import {faAd} from '@fortawesome/free-solid-svg-icons';
import { faImages} from '@fortawesome/free-regular-svg-icons';
import _ from 'underscore';
library.add(faImages, faAd)

export default {
  props: [
    'editor',
    'insert',
    'file_input_name',
    'imgur_bool',
    'editorRef',
    'handler'
  ],
  components: {
    FontAwesomeIcon
  },
  data() {
    return {
      currentLine: null,
      currentBtn: null,
      position: {
        top: '0'
      },
      isShow: false,
      link: '',
      newTab: false
    }
  },
  methods: {
    initializeExisting() {
      const handlerVm = this;
      setTimeout(() => {
        const focused = this.editor.getFocusedElement()
        if(!focused) return false;
        // const editorBtn = focused.getElementsByClassName('editor-btn-form')
        const editorBtn = focused.getElementsByClassName('editor-btn')
        _.map(editorBtn, (elm) => {
          // Set hover
          elm.addEventListener('mouseover', function () {
            setTimeout(() => {
              handlerVm.btnSettingsHandler(this)
            })
          });
        })
      })
    },
    addButton() {
      let _this = this;
      if (this.insert.isToggle) {
        const handlerVm = this
        this.editorRef.focus();
        this.editor.selectElement(this.insert.focusLine);
        this.editor.pasteHTML(`<input class="editor-btn" type="button" :link="https://google.com" :newTab="_self" onclick="location.href='https://google.com';" value="Hover to edit" />`
            , {cleanAttrs: [], cleanTags: [], unwrapTags: []})
//         this.editor.pasteHTML(`<form action="https://google.com" class="editor-btn-form">
//     <input type="submit" value="text" class="editor-btn" />
// </form>`, {cleanAttrs: [], cleanTags: [], unwrapTags: []})
      }
      this.initializeExisting();
    },
    btnSettingsHandler(elm) {
      const btn = elm;
      this.currentLine = elm;
      this.isShow = true;
      const currentPos = btn.getBoundingClientRect();
      this.position.top = currentPos.top + 'px';
      // let left = elm.getElementsByTagName('input')[0].getBoundingClientRect().right;
      this.position.left = elm.getBoundingClientRect().right + 150 + 'px';
      this.$emit('btnHover', {
        url: this.url,
        currentLine: this.currentLine,
        isShow: this.isShow,
        position: this.position
      })
      this.insert.isShow = false;
    }
  },
  mounted() {
    this.initializeExisting()
  },
  beforeMount () {
    this.window = window;
    window.addEventListener('scroll', this.handleScroll);
  },
  beforeDestroy () {
    window.removeEventListener('scroll', this.handleScroll);
  }
}
</script>
<style>
.editor-btn{
  background-color: grey;
  border-radius: 25px;
  padding: 10px 20px;
  color: white;
  border: none;
}

</style>
