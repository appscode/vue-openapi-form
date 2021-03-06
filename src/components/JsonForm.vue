<template>
  <div>
    <!-- tabs start  -->
    <div class="tabs ac-tabs is-boxed mt-10 mb-0">
      <ul>
        <li :class="{ 'is-active': activeTab === 'file' }">
          <a @click.prevent="activeTab = 'file'">
            <span>File</span>
          </a>
        </li>
        <li :class="{ 'is-active': activeTab === 'preview-changes' }">
          <a @click.prevent="activeTab = 'preview-changes'">
            <span>Preview Changes</span>
          </a>
        </li>
      </ul>
    </div>
    <div v-if="activeTab === 'file'">
      <monaco-editor
        ref="monacoEditor"
        @editorDidMount="onEditorMount"
        v-model="valueString"
        :options="editorOptions"
        :diff-editor="activeTab === 'preview-changes'"
        language="yaml"
        class="editor-writable vh-80 is-clipped"
      />
    </div>

    <div v-else>
      <monaco-editor
        :key="activeTab"
        ref="monacoDiffEditor"
        @editorDidMount="onDiffEditorMount"
        class="editor-writable vh-80 is-clipped"
        :options="editorOptions"
        :value="valueString"
        :diff-editor="true"
        :original="originalValueString"
        language="yaml"
      />
    </div>
  </div>
</template>

<script>
import { model } from "../mixins/model.js";
import jsyaml from "js-yaml";
import MonacoEditor from "vue-monaco";
import monacoEditorThemes from "@/plugins/monaco-editor-themes.js";
import { mapGetters } from "vuex";

export default {
  name: "yaml-form",
  props: {
    value: {
      type: null,
      default: () => ({}),
    },
  },

  mixins: [model],

  components: {
    MonacoEditor,
  },

  data() {
    return {
      activeTab: "file",

      valueString: "",

      editorOptions: {
        minimap: {
          enabled: true,
        },
        readOnly: false,
      },
      diffEditorOptions: {
        minimap: {
          enabled: true,
        },
        readOnly: true,
      },
    };
  },

  computed: {
    ...mapGetters({
      editorTheme: "editorTheme",
    }),
    originalValueString() {
      return JSON.stringify(this.referenceModel, null, 2);
    },
  },

  methods: {
    initValueString() {
      this.valueString = JSON.stringify(this.value, null, 2);
    },

    updateModelData() {
      let ans = null;
      try {
        ans = jsyaml.safeLoad(this.valueString, {
          json: true,
        }); // yaml => jsObject
      } catch (e) {
        ans = this.modelData;
      }

      this.modelData = ans;
      this.$emit("code::model-data-updated", ans);
    },

    onEditorMount() {
      const editor = this.$refs.monacoEditor.getEditor();

      // add event listeners
      editor.onDidBlurEditorText(this.updateModelData);

      // set theme
      monacoEditorThemes.setTheme(
        this.$refs.monacoEditor.monaco.editor,
        this.editorTheme
      );
    },

    onDiffEditorMount() {
      // set theme
      monacoEditorThemes.setTheme(
        this.$refs.monacoDiffEditor.monaco.editor,
        this.editorTheme
      );
    },
  },

  created() {
    this.initValueString();
  },

  watch: {
    value() {
      this.initValueString();
    },
  },
};
</script>
