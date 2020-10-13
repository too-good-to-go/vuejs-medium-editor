<template>
  <div class="image-handler-container">
    <div class="insert-image-container" v-show="insert.isShow" v-bind:style="insert.position">
      <div class="insert-image-toggle">
        <button v-on:click="toggle" class="btn-toggle">
          <font-awesome-icon icon="plus"/>
        </button>
      </div>
      <div class="insert-image-menu" v-show="insert.isToggle">
        <insert-image
            :editor="editor"
            :insert="insert"
            :editorRef="editorRef"
            :uploadUrl="uploadUrl"
            :uploadUrlHeader="uploadUrlHeader"
            :file_input_name="file_input_name"
            :imgur_bool="imgur_bool"
            :handler="handler"
            v-on:uploaded="uploadCallback"
            v-on:imageClick="imageClickHandler"
            title="Insert Image"
        ></insert-image>
        <insert-video :editor="editor"
                      :editorRef="editorRef"
                      v-on:onChange="onChange" :insert="insert" title="Insert video"></insert-video>

        <insert-button  :editor="editor"
                        :insert="insert"
                        :editorRef="editorRef"
                        :uploadUrl="uploadUrl"
                        :uploadUrlHeader="uploadUrlHeader"
                        :file_input_name="file_input_name"
                        :handler="handler"
                        @btnHover="btnClickHandler"
                        title="Insert Button"></insert-button>
      </div>
    </div>

    <image-position
        :handler="handler"
        v-on:onPositionChange="onChange"
    ></image-position>
    <button-settings :handler="handlerBtn"
                     v-on:onButtonChange="onChange"
                     @closeSettings="closeSettings">
    </button-settings>
  </div>
</template>

<script>
import InsertImage from './Embed/InsertImage';
import InsertVideo from './Embed/InsertVideo';
import InsertButton from "@/libs/Embed/InsertButton";
import ImagePosition from './Embed/ImagePosition';
import {library} from '@fortawesome/fontawesome-svg-core';
import {FontAwesomeIcon} from '@fortawesome/vue-fontawesome';
import {faPlus} from '@fortawesome/free-solid-svg-icons';
import _ from 'underscore';
import ButtonSettings from "@/libs/Embed/ButtonSettings";

library.add(faPlus)
export default {
  components: {
    FontAwesomeIcon,
    InsertImage,
    InsertVideo,
    ImagePosition,
    ButtonSettings,
    InsertButton
  },
  data() {
    return {
      insert: {
        position: {
          top: '0',
          left: '0'
        },
        isShow: false,
        isToggle: false,
        embedElm: null,
        files: [],
        focusLine: null
      },
      handler: {
        currentLine: null,
        currentImg: null,
        currentSize: 'is-normal',
        position: {
          top: '0'
        },
        isShow: false
      },
      handlerBtn: {
        currentLine: null,
        currentBtn: null,
        position: {
          top: '0'
        },
        isShow: false
      }
    }
  },
  props: [
    'editor',
    'uploadUrl',
    'uploadUrlHeader',
    'file_input_name',
    'imgur_bool',
    'editorRef',
    'onChange'
  ],
  methods: {
    subscribe() {
      this.editor.subscribe('editableKeyup', this.detectShowToggle)
      this.editor.subscribe('editableClick', this.detectShowToggle)
      this.editor.subscribe('editableKeyup', this.detectImageDescription)
      this.editor.subscribe('editableClick', this.detectImageDescription)
    },
    unsubscribe() {
      this.editor.unsubscribe('editableKeyup', this.detectShowToggle)
      this.editor.unsubscribe('editableClick', this.detectShowToggle)
      this.editor.unsubscribe('editableKeyup', this.detectImageDescription)
      this.editor.unsubscribe('editableClick', this.detectImageDescription)
    },
    detectImageDescription() {
      const focused = this.editor.getFocusedElement()
      if (!focused) return;
      const editorImages = focused.getElementsByClassName('editor-media-description')
      _.map(editorImages, (elm) => {
        const description = elm.innerHTML.trim()
        if (!description || description == "<br>") {
          elm.className = 'editor-media-description is-empty'
        } else {
          elm.className = 'editor-media-description'
        }
      })
    },
    detectShowToggle(e) {
      if (this.insert.isShow && this.insert.isToggle) {
        this.toggle();
      }
      if (e.keyCode == 13) {
        const focused = this.editor.getSelectedParentElement()
        const nextElm = focused.nextElementSibling
        const prevElm = focused.previousElementSibling
        if (nextElm && prevElm && nextElm.className.indexOf('editor-media-description') > -1 && prevElm.className.indexOf('editor-media') > -1) {
          nextElm.parentNode.insertBefore(nextElm, focused);
        }
      }
      this.handler.isShow = false
      if (e.target.className.indexOf('editor-media-description') <= -1) {
        const editorImages = this.editor.getFocusedElement().getElementsByClassName('editor-media')
        _.map(editorImages, (imgElm) => {
          imgElm.className = imgElm.className.replace('is-focus', '')
        })
      }
      const currentLine = this.editor.getSelectedParentElement()
      const outerHtml = currentLine.outerHTML
      const isList = outerHtml.indexOf('<li>') > -1
      const content = currentLine.innerHTML.replace(/^(<br\s*\/?>)+/, '').trim()
      if (content || isList) {
        // Not show toggle if focus line has content & list
        this.insert.isShow = false
        this.insert.isToggle = false
        this.insert.focusLine = null
      } else {
        const currentPos = currentLine.getBoundingClientRect();
        this.insert.position.top = currentPos.top + 'px'
        this.insert.position.left = currentPos.left + 'px'
        this.insert.isShow = true
        this.insert.focusLine = currentLine
      }
    },
    toggle() {
      this.insert.isToggle = !this.insert.isToggle;
    },
    closeSettings(){
      this.handlerBtn.isShow = false;
    },
    imageClickHandler(value) {
      this.handler = value
    },
    btnClickHandler(value) {
     this.handlerBtn = value
    },
    uploadCallback(url) {
      this.$emit('uploaded', url)
    },
    handleScroll() {
      this.handlerBtn.isShow = false;
      this.handler.isShow = false;
      if (this.insert.isShow) {
        const currentLine = this.editor.getSelectedParentElement()
        const currentPos = currentLine.getBoundingClientRect()
        this.insert = {
          ...this.insert,
          isShow: true,
          focusLine: currentLine,
          position: {
            top: currentPos.top + 'px',
            left: currentPos.left + 'px'
          }
        }
      }
    }
  },
  mounted() {
    this.subscribe()
  },
  destroyed() {
    this.unsubscribe()
  },
  beforeMount() {
    this.window = window;
    window.addEventListener('scroll', this.handleScroll);
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll);
  }
}
</script>
