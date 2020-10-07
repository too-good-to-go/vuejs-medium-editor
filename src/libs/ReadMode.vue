<template>
    <div class="medium-editor-container">
        <div class="editor read-only has-content" ref="content" v-html="content">
        </div>
    </div>
</template>

<script>
import _ from 'underscore';

export default {
    props: [
        'content'
    ],
    methods: {
        render() {
            this.renderEmbed()
        },
        renderEmbed() {
            const editorEmbeds = this.$refs.content.getElementsByClassName("editor-embed");
            _.map(editorEmbeds, (elm) => {
                const link = elm.getElementsByTagName('a')[0]
                const nextElm = elm.nextElementSibling

                if(link) {
                    const url = link.getAttribute('href')
                    this.renderEmbedElm(url, elm)
                }
            })
        }
    },
    mounted() {
        this.render();
    }
}
</script>
