<template>
  <div>
    <button class="btn-toggle" v-on:click="videoInput = !videoInput">
      <font-awesome-icon :icon="['fas', 'video']"/>
    </button>
    <div v-if="videoInput">
      <label>
        <input v-model="videoUrl" placeholder="Video url (youtube or vimeo)"/>
      </label>
      <button v-on:click="addVideo()">
        Add video
      </button>
    </div>

  </div>
</template>

<script>
import {library} from '@fortawesome/fontawesome-svg-core';
import {FontAwesomeIcon} from '@fortawesome/vue-fontawesome';
import {faVideo} from '@fortawesome/free-solid-svg-icons';

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
      isShow: false
    }
  },
  components: {
    FontAwesomeIcon
  },
  methods: {
    parseVideo(url) {
      // - Supported YouTube URL formats:
      //   - http://www.youtube.com/watch?v=My2FRPA3Gf8
      //   - http://youtu.be/My2FRPA3Gf8
      //   - https://youtube.googleapis.com/v/My2FRPA3Gf8
      // - Supported Vimeo URL formats:
      //   - http://vimeo.com/25451551
      //   - http://player.vimeo.com/video/25451551
      // - Also supports relative URLs:
      //   - //player.vimeo.com/video/25451551

      url.match(/(http:|https:|)\/\/(player.|www.)?(vimeo\.com|youtu(be\.com|\.be|be\.googleapis\.com))\/(video\/|embed\/|watch\?v=|v\/)?([A-Za-z0-9._%-]*)(\&\S+)?/);

      if (RegExp.$3.indexOf('youtu') > -1) {
        var type = 'youtube';
      } else if (RegExp.$3.indexOf('vimeo') > -1) {
        var type = 'vimeo';
      }

      let videoObj = {
        type: type,
        id: RegExp.$6
      };
      if (videoObj.type === 'youtube') {
        return '//www.youtube.com/embed/' + videoObj.id;
      } else if (videoObj.type === 'vimeo') {
        return '//player.vimeo.com/video/' + videoObj.id;
      }
    },
    addVideo() {
      let _this = this;
      this.$emit('uploaded', _this.videoUrl);
      if (this.insert.isToggle) {
        const handlerVm = this
        this.editorRef.focus();
        this.editor.selectElement(this.insert.focusLine);
        let videoUrl = this.parseVideo(_this.videoUrl)
        this.editor.pasteHTML(`<div class="editor-media">
                        <iframe src="${videoUrl}" frameborder="0" allowfullscreen></iframe>
                    </div>
                    <div class="editor-media-description"><br></div>
                    <br />`, {cleanAttrs: [], cleanTags: [], unwrapTags: []})
        this.currentLine = this.editor.getSelectedParentElement().previousElementSibling.previousElementSibling
        this.currentVideo = this.currentLine.querySelector('iframe')
        const currentPos = this.currentVideo.getBoundingClientRect();
        this.window.scrollTo(0, currentPos.top - currentPos.x);
        this.insert.isShow = false;
      }
    }
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
