<template>
  <validation-observer
    tag="form"
    :ref="`${schema.title.replace(/ /g, '-')}-observer`"
    :vid="`${schema.title.replace(/ /g, '-')}-observer`"
    v-slot="{ errors: observerErrors }"
    class="ac-nested-elements object-form-wrapper"
    :class="{ 'stop-line': isLastChild, 'hide-upper-line': isRoot }"
  >
    <!-- {{ calcObserverError(observerErrors) }} -->
    <div class="nested-header mb-5">
      <h6 class="is-flex is-semi-normal">
        <div
          v-if="!isRoot"
          class="collaps-icon"
          :disabled="activeTab !== 'form'"
          @click.prevent="toggleFold()"
        >
          <i
            :class="['fa', isFolded ? 'fa-plus' : 'fa-minus']"
            aria-hidden="true"
          ></i>
        </div>
        {{ schema.title || "Array Item Description" }}
        <!-- show errors-->
        <component-errors
          :errors="[...errors, ...calcObserverError(observerErrors)]"
        />
      </h6>
      <tabs v-if="!onlyJson" v-model="activeTab" />
      <!-- 
          <button
            v-if="isRoot"
            class="done-button button ac-button is-primary is-pulled-right"
            :class="{ 'is-loading': isFormSubmitting }"
            @click.prevent="submit()"
          >
            <span>DONE</span>
          </button> -->
    </div>
    <!-- form for all the object's properties -->
    <object-form
      v-show="!onlyJson && activeTab === 'form'"
      :key="`${schema.title}-form`"
      :properties="schema.properties"
      :title="schema.title"
      :required="schema.required"
      :isSelfRequired="isSelfRequired"
      :type="schema.type"
      :isRoot="isRoot"
      :isSelfFolded="isRoot ? false : isFolded"
      v-model="modelData"
      :reference-model="referenceModel || {}"
    />
    <!-- declared in tabs component -->
    <yaml-form
      v-if="activeTab === 'yaml'"
      v-model="modelData"
      :reference-model="referenceModel || {}"
    />
    <json-form
      v-else-if="activeTab === 'json'"
      v-model="modelData"
      :reference-model="referenceModel || {}"
    />
  </validation-observer>
</template>

<script>
import { model } from "../mixins/model.js";
import fold from "../mixins/fold.js";
import tabs from "../mixins/tabs.js";
import validation from "../mixins/validation.js";

export default {
  name: "object-form-wrapper",
  props: {
    schema: {
      type: Object,
      default: () => ({}),
    },
    value: {
      type: Object,
      default: () => ({}),
    },
    isRoot: {
      type: Boolean,
      default: false,
    },
    errors: {
      type: Array,
      default: () => [],
    },
    isLastChild: {
      type: Boolean,
      default: false,
    },
    onlyJson: {
      type: Boolean,
      default: false,
    },
    isFormSubmitting: {
      type: Boolean,
      default: false,
    },
  },

  methods: {
    submit() {
      this.$emit("vof:submitted");
    },
  },

  mixins: [model, fold, tabs, validation],
};
</script>
