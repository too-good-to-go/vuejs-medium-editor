<template>
  <div>
    <button class="btn-toggle" v-on:click="videoInput = !videoInput">
      <font-awesome-icon :icon="['fas', 'video']" />
    </button>
    <div v-if="videoInput">
      <input v-model="videoUrl"/>
      <button v-on:click="addVideo('vimeo')">Add Vimeo video</button>
      <button v-on:click="addVideo('yt')">Add Youtube video</button>
    </div>

  </div>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faVideo } from '@fortawesome/free-solid-svg-icons';


library.add(faVideo)

export default {
  props: [
    'editor',
    'editorRef',
    'insert'
  ],
  data() {
    return {
      videoUrl: '',
      videoInput: false,
      embedElm: null,
      currentLine: null,
      currentVideo: null,
      currentSize: 'is-normal',
      position: {
        top: '0'
      },
      urls: {
        yt: 'https://www.youtube.com/embed/',
        vimeo: '//player.vimeo.com/video/'
      },
      isShow: false
    }
  },
  components: {
    FontAwesomeIcon
  },
  methods: {
    addVideo(platform) {
      let _this = this;
      this.$emit('uploaded', _this.videoUrl);
      if(this.insert.isToggle) {
        const handlerVm = this
        this.editorRef.focus()
        this.editor.selectElement(this.insert.focusLine);
        let vUrl = eval('this.urls.' + platform) + _this.videoUrl
        this.editor.pasteHTML(`<div class="editor-media">
                        <iframe src="${vUrl}" frameborder="0" allowfullscreen></iframe>
                    </div>
                    <div class="editor-media-description"><br></div>
                    <br />`, { cleanAttrs: [], cleanTags: [], unwrapTags: []})
        this.currentLine = this.editor.getSelectedParentElement().previousElementSibling.previousElementSibling
        this.currentVideo = this.currentLine.querySelector('iframe')
        const currentPos = this.currentVideo.getBoundingClientRect();
        this.window.scrollTo(0, currentPos.top - currentPos.x);
        this.currentLine.onclick = function() {
          setTimeout(() => {
            handlerVm.sizingHandler(this)
          })
        }
        this.insert.isShow = false;
      }
    },
    addEmbed() {

      console.log('add')
    },
    inputFile(newFile, oldFile) {
      if (newFile && !oldFile) {
        this.$refs.upload.active = true
      }

      // Image Upload Successful
      if(newFile && newFile.success) {
        if(this.imgur_bool) {
          this.addVideo(newFile.response.data.link)
        } else {
          this.addVideo(newFile.response.url)
        }
      }
    }
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
