<template>
  <div ref="appearanceEditor" id="appearance-editor" class="html-editor"></div>
</template>

<script>
import CodeFlask from 'codeflask';

export default {
  props: {
    value: String,
    language: String,
  },

  data() {
    return {
      data: '',
      flask: null,
    };
  },

  methods: {
    initAppearanceEditor(body) {
      // CodeFlask editor is rendered in a shadow DOM tree to keep its styles
      // sandboxed away from the global styles.
      const el = document.createElement('code-flask');
      el.attachShadow({ mode: 'open' });
      el.shadowRoot.innerHTML = `
        <style>
          .codeflask .codeflask__textarea { background-color: #f5f5f5; color: transparent !important; caret-color: #111 !important; }
          .codeflask .codeflask__flatten { font-size: 15px; }
          .codeflask .codeflask__lines { background: transparent; z-index: 10; }
          .codeflask .token.tag { font-weight: bold; }
          .codeflask { color: #4f559c; }
          .codeflask .token.punctuation { color: #4a4a4a; }
          .codeflask .token.keyword,
          .codeflask .token.function,
          .codeflask .token.boolean,
          .codeflask .token.number,
          .codeflask .token.selector,
          .codeflask .token.property,
          .codeflask .token.tag,
          .codeflask .token.attr-value { color: #8500ff; }
          .codeflask .token.operator { color: #ff5598; }
          .codeflask .token.string { color: #41ad8f; }
          .codeflask .token.comment { color: #9badb7; }
          @media (prefers-color-scheme: dark) {
            .codeflask .codeflask__textarea { background-color: #333; color: transparent !important; caret-color: #fff !important; }
            .codeflask { color: #cfe664; }
            .codeflask .token.punctuation { color: #64e6bc; }
            .codeflask .token.keyword,
            .codeflask .token.function,
            .codeflask .token.boolean,
            .codeflask .token.number,
            .codeflask .token.selector,
            .codeflask .token.property,
            .codeflask .token.tag,
            .codeflask .token.attr-value { color: #e6648e; }
            .codeflask .token.operator { color: #e67b64; }
            .codeflask .token.string { color: #41ad8f; }
            .codeflask .token.comment { color: #64cfe6;}
        }
        </style>
        <div id="area"></area>
      `;
      this.$refs.appearanceEditor.appendChild(el);

      this.flask = new CodeFlask(el.shadowRoot.getElementById('area'), {
        language: this.language,
        lineNumbers: false,
        styleParent: el.shadowRoot,
        defaultTheme: false,
      });

      // Set the initial value.
      this.flask.updateCode(body);
    },
  },

  mounted() {
    if (this.$props.value) {
      this.initAppearanceEditor(this.$props.value || '');
    } else {
      this.initAppearanceEditor('');
    }

    // Wait for view to finish loading before adding the update listener
    this.$nextTick(() => {
      this.flask.onUpdate((v) => {
        this.data = v;
        this.$emit('input', v);
      });
    });
  },

  watch: {
    value(newVal) {
      if (newVal !== this.data) {
        if (newVal) {
          this.flask.updateCode(newVal);
        } else {
          this.flask.updateCode('');
        }
      }
    },
  },
};

</script>
