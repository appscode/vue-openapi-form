<template>
  <div class="ac-single-input is-small is-information">
    <template v-if="ui.tag === 'input'">
      <template v-if="ui.type === 'checkbox'">
        <div class="ac-single-switch is-flex pb-10">
          <input
            :id="schema.title.replace(' ', '-')"
            type="checkbox"
            name="switchRoundedDefault"
            class="switch ac-switch is-rounded is-primary"
            v-model="modelData"
          />
          <label class="switch-label" :for="schema.title.replace(' ', '-')">{{
            schema.title
          }}</label>
        </div>
      </template>
      <template v-else>
        <label
          @click.prevent="focusInput()"
          :class="[labelShow ? 'show-label' : '', 'ac-label']"
          >{{ schema.title }}</label
        >
        <div v-if="ui.tag === 'input'">
          <input
            ref="inputField"
            class="ac-input"
            :type="ui.type"
            :class="{
              'is-success': validationOb.validated && validationOb.valid,
              'is-danger': validationOb.validated && validationOb.invalid,
              'bg-white': modelData,
            }"
            :placeholder="ui.placeholder || ''"
            v-model="modelData"
            @change="modelData = $event.target.value"
            @focus="triggerInput()"
            @focusout="unTriggerInput()"
          />
          <template v-if="validationOb.validated">
            <button class="button is-information" v-if="validationOb.valid">
              <i class="fa fa-check"></i>
            </button>
            <button
              class="button is-information is-warning"
              v-if="validationOb.invalid"
            >
              <i class="fa fa-times"></i>
            </button>
          </template>
          <p
            class="is-error"
            v-if="
              validationOb &&
              validationOb.errors &&
              validationOb.errors.length > 0
            "
          >
            <span class="warning"><i class="fa fa-warning"></i></span>
            {{ validationOb.errors[0] }}
          </p>
        </div>
      </template>
    </template>

    <template v-if="ui.tag === 'textarea'">
      <textarea
        class="input"
        :type="ui.type"
        :class="{
          'is-success': validationOb.validated && validationOb.valid,
          'is-danger': validationOb.validated && validationOb.invalid,
        }"
        :placeholder="ui.placeholder || ''"
        v-model="modelData"
        @change="modelData = $event.target.value"
      />
      <template v-if="validationOb.validated">
        <button class="button is-information is-success" v-if="valid">
          <i class="fa fa-check"></i>
        </button>
        <button class="button is-information is-warning" v-if="invalid">
          <i class="fa fa-times"></i>
        </button>
      </template>
      <span class="is-warning" v-if="validationOb.errors.length > 0">
        <i class="fa fa-warning warning"></i>
        {{ validationOb.errors[0] }}
      </span>
    </template>

    <!-- <div v-if="ui.tag === 'checkbox'" class="field">
      <label>Switch rounded default</label>
      <input
        type="checkbox"
        name="switchRoundedDefault"
        class="switch is-rounded"
        v-model="modelData"
      />
    </div> -->
    <!-- <p class="help is-danger">This username is available</p> -->
  </div>
</template>

<script>
import { model } from "../mixins/model.js";
import validation from "../mixins/validation.js";
import size from "../mixins/size.js";

export default {
  name: "simple-input",
  data() {
    return {
      labelShow: false,
      isIntegerSetToNull: false,
    };
  },
  methods: {
    // to float up label when input is focused
    triggerInput() {
      this.labelShow = true;
    },
    // to float down label when input is unfocused and value field is empty
    unTriggerInput() {
      if (!this.modelData) this.labelShow = false;
    },
    // to float up label and input field is focused when label is clicked in placeholder mode
    focusInput() {
      this.labelShow = true;
      const inputField = this.$refs.inputField;
      inputField.focus();
    },
  },

  props: {
    schema: {
      type: Object,
      default: () => ({}),
    },
    value: {
      default: "",
    },
    validationOb: {
      type: Object,
      default: () => ({}),
    },
  },

  mounted() {
    if (this.modelData) this.labelShow = true;
  },

  mixins: [model, validation, size],

  computed: {
    ui() {
      return this.schema.ui || { tag: "input", type: "text" };
    },
  },

  watch: {
    modelData: {
      immediate: true,
      deep: true,
      handler(newVal, oldVal) {
        if (newVal) this.labelShow = true;
        else this.labelShow = false;

        if (
          this.isIntegerSetToNull ||
          (oldVal !== null && oldVal !== undefined)
        ) {
          if (this.isIntegerSetToNull && newVal) {
            this.isIntegerSetToNull = false;
          }
          if (this.type === "number" || this.type === "integer") {
            // if the newVal string is empty, emit null
            if (newVal === "") {
              this.isIntegerSetToNull = true;
              this.$emit("input", null);
            } else this.$emit("input", +newVal);
          } else this.$emit("input", newVal);
        }
      },
    },
  },
};
</script>
