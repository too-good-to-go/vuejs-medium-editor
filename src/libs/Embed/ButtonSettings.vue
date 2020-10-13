<template>
  <div class="button-handler" v-if="handler.isShow" :style="{top: handler.position.top, left: handler.position.left}">
    <div class="image-hander-menu">
      <label>
        <small>link</small>
        <input v-model="link"/>
      </label>
      <label>
        <small>text</small>
        <input v-model="text"/>
      </label>
      <label for="newTab"><small>New tab</small></label><input type="checkbox" v-model="newTab" id="newTab"/>
      <div class="confirm-box">
        <button class="btn-toggle btn-success" v-on:click="editBtn">
          save
        </button>
        <button class="btn-toggle btn-cancel" v-on:click="cancel">
          cancel
        </button>
      </div>

    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      text: '',
      link: '',
      newTab: ''
    }
  },
  props: [
    'handler'
  ],
  watch:{
    handler: function (oldVal, newVal) {
      this.setNewHandler();
    }
  },
  methods: {
    cancel() {
      this.$emit('closeSettings');
    },
    setNewHandler(){
      this.text = this.$props.handler.currentLine.value;
      this.link = this.$props.handler.currentLine.getAttribute(':link');
      this.newTab = this.$props.handler.currentLine.getAttribute(':newTab') === '_blank' ;
    },
    editBtn() {
      let _this = this;
     // this.handler.currentLine.action =  _this.link;
      this.handler.currentLine.value = _this.text;
      this.handler.currentLine.setAttribute(':link', this.link);
      this.handler.currentLine.setAttribute(':newTab', this.newTab ? '_blank': '_self');
      let onClick = this.newTab ? 'window.open("' + this.link + '","_blank")' : 'window.open("' + this.link + '","_self")';
      this.handler.currentLine.setAttribute('onclick', onClick);
      this.$emit('closeSettings');
    }
  }
}
</script>
<style>
.button-handler {
  color: white;
  border-radius: 10px;
  padding: 20px;
  width: min-content;
  display: flex;
  position: fixed;
  left: 100%;
  top: 100px;
  transform: translate(-50%, -20px);
  background-color: #114d4de3;

}
.confirm-box button{
  margin-top:10px;
  padding: 10px 15px;
  border-radius: 24px;
  border: none;
  font-weight: bold;
  text-transform: uppercase;
}
.btn-success{
  color: white;
  background-color: #49ada1;
  margin-right: 20px;
}
.btn-cancel{
  color: #7a7a7a;
  background-color: #d8d8d8;
}
.image-hander-menu input{
  margin-left: 10px;
}
</style>

