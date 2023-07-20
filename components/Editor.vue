<script setup lang="ts">
import { EditorState } from '@codemirror/state';
import {
  EditorView,
  keymap,
  highlightActiveLine,
  lineNumbers,
  highlightActiveLineGutter
} from '@codemirror/view';
import { defaultKeymap, history, historyKeymap } from '@codemirror/commands';
import {
  indentOnInput,
  bracketMatching,
  syntaxHighlighting,
  HighlightStyle
} from '@codemirror/language';
import { tags } from '@lezer/highlight';
import { markdown, markdownLanguage } from '@codemirror/lang-markdown';
import { javascript } from '@codemirror/lang-javascript';
import { languages } from '@codemirror/language-data';
import { oneDark } from '@codemirror/theme-one-dark';
import { ref, onMounted } from 'vue';

const viewContainer = ref();
const editorState = ref();

const props = defineProps({
  initialDoc: String
});

const transparentTheme = EditorView.theme({
  '&': {
    backgroundColor: 'transparent !important',
    height: '100%'
  }
});

const syntaxHightlight = HighlightStyle.define([
  {
    tag: tags.heading1,
    fontSize: '1.8em',
    fontWeight: 'bold'
  },
  {
    tag: tags.heading2,
    fontSize: '1.7em',
    fontWeight: 'bold'
  },
  {
    tag: tags.heading3,
    fontSize: '1.6em',
    fontWeight: 'bold'
  },
  {
    tag: tags.heading4,
    fontSize: '1.5em',
    fontWeight: 'bold'
  },
  {
    tag: tags.heading5,
    fontSize: '1.4em',
    fontWeight: 'bold'
  },
  {
    tag: tags.heading6,
    fontSize: '1.3em',
    fontWeight: 'bold'
  }
]);

const startState = EditorState.create({
  doc: props.initialDoc,
  extensions: [
    keymap.of([...defaultKeymap, ...historyKeymap]),
    lineNumbers(),
    highlightActiveLineGutter(),
    history(),
    indentOnInput(),
    bracketMatching(),
    highlightActiveLine(),
    syntaxHighlighting(syntaxHightlight),
    markdown({
      base: markdownLanguage,
      codeLanguages: languages,
      addKeymap: true
    }),
    javascript(),

    oneDark,
    transparentTheme,
    EditorView.lineWrapping,
    EditorView.updateListener.of((update) => {
      if (update.changes) {
        editorState.value = update.state;
      }
    })
  ]
});
const view = () => {
  new EditorView({
    state: startState,
    parent: viewContainer.value
  });
};
onMounted(() => {
  view();
});
</script>

<template>
  <div ref="viewContainer"></div>
</template>

<style scoped>
.viewContainer {
  width: 100%;
  height: 100%;
}
</style>
