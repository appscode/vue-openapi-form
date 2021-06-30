<template>
  <validation-observer
    tag="div"
    :ref="`${schema.title.replace(/ /g, '-')}-observer`"
    :vid="`${schema.title.replace(/ /g, '-')}-observer`"
    v-slot="{ errors: observerErrors }"
    class="ac-nested-elements key-value-pairs"
    :class="{ 'stop-line': isLastChild }"
    :key="updatePass"
  >
    <div class="nested-header mb-5">
      <h6 class="is-flex is-semi-normal">
        <div class="collaps-icon is-disabled">
          <i aria-hidden="true" class="fa fa-minus"></i>
        </div>
        {{ schema.title || "Array Item Description"
        }}<!-- show errors-->
        <component-errors
          :errors="[...errors, ...calcObserverError(observerErrors)]"
        />
      </h6>
      <tabs v-model="activeTab" />
    </div>
    <!-- existing key values -->
    <template v-if="activeTab === 'form'">
      <div
        class="mb-15"
        v-for="(prop, index) in keyValueArray"
        :key="`${index}-${schema.title}-form`"
      >
        <key-value-pair-items
          v-model="keyValueArray[index]"
          :reference-model="referencekeyValueArray[index] || {}"
          :index="index"
          :schema="schema"
          :additionalProperties="additionalProperties"
        />
      </div>
      <!-- key input -->
      <validation-observer
        :ref="`${schema.title.replace(/ /g, '-')}-new`"
        :vid="`${schema.title.replace(/ /g, '-')}-new-observer`"
        :disabled="true"
        tag="div"
        class="key-value-save"
      >
        <validation-provider
          :vid="`${schema.title.replace(/ /g, '-')}-key-provider`"
          rules="required"
          :name="`${schema.title.replace(/ /g, '-')}-key`"
          v-slot="validationOb"
          tag="div"
        >
          <simple-input
            :schema="{
              title: 'Key',
              type: 'string',
              ui: { tag: 'input', type: 'text' },
            }"
            :type="`string`"
            :validationOb="validationOb"
            v-model="newKey"
            :reference-model="''"
          />
        </validation-provider>

        <!-- new value input -->
        <!-- if value is object -->
        <template v-if="additionalProperties.type === 'object'">
          <validation-provider
            v-slot="{ errors }"
            rules="required"
            :name="`${schema.title.replace(/ /g, '-')}-value`"
            :vid="`${schema.title.replace(/ /g, '-')}-value-provider`"
            slim
          >
            <object-form-wrapper
              :is-last-child="true"
              :isSelfRequired="true"
              :schema="additionalProperties"
              :type="additionalProperties.type"
              :errors="errors"
              v-model="newValue"
              :reference-model="{}"
            />
          </validation-provider>
        </template>
        <!-- if value is key value pairs -->
        <template v-else-if="additionalProperties.type === 'key-value-pairs'">
          <validation-provider
            v-slot="{ errors }"
            rules="required"
            :name="`${schema.title.replace(/ /g, '-')}-value`"
            :vid="`${schema.title.replace(/ /g, '-')}-value-provider`"
            slim
          >
            <key-value-pairs
              :is-last-child="true"
              :schema="additionalProperties"
              :type="additionalProperties.type"
              :errors="errors"
              v-model="newValue"
              :reference-model="{}"
            />
          </validation-provider>
        </template>
        <!-- if value is array -->
        <template v-else-if="additionalProperties.type === 'array'">
          <validation-provider
            v-slot="{ errors }"
            rules="required"
            :name="`${schema.title.replace(/ /g, '-')}-value`"
            :vid="`${schema.title.replace(/ /g, '-')}-value-provider`"
            slim
          >
            <array-input
              :is-last-child="true"
              :schema="additionalProperties"
              :type="additionalProperties.type"
              :errors="errors"
              v-model="newValue"
              :reference-model="[]"
            />
          </validation-provider>
        </template>
        <!-- if value is simple input -->
        <template v-else>
          <validation-provider
            v-slot="validationOb"
            rules="required"
            :name="`${schema.title.replace(/ /g, '-')}-value`"
            :vid="`${schema.title.replace(/ /g, '-')}-value-provider`"
            slim
          >
            <simple-input
              :schema="additionalProperties"
              :type="additionalProperties.type"
              :validationOb="validationOb"
              v-model="newValue"
              :reference-model="''"
            />
          </validation-provider>
        </template>
        <button
          class="button ac-button is-small is-square is-outlined-gray is-transparent"
          :class="{ 'is-small': !isMedium }"
          @click.prevent="addProp()"
        >
          <i class="fa fa-plus"></i>
        </button>
      </validation-observer>
    </template>
    <!-- declared in tabs component -->
    <yaml-form
      v-if="activeTab === 'yaml'"
      @code::model-data-updated="updateKeyValueArray"
      v-model="modelData"
      :reference-model="referenceModel || {}"
    />
    <!-- declared in tabs component -->
    <json-form
      v-else-if="activeTab === 'json'"
      @code::model-data-updated="updateKeyValueArray"
      v-model="modelData"
      :reference-model="referenceModel || {}"
    />
  </validation-observer>
</template>

<script>
import { model } from "../mixins/model.js";
import tabs from "../mixins/tabs.js";
import validation from "../mixins/validation.js";
import size from "../mixins/size.js";
import KeyValuePairItems from "./sub-components/KeyValuePairItems.vue";

export default {
  name: "key-value-pairs",
  props: {
    schema: {
      type: Object,
      default: () => ({}),
    },
    value: {
      type: Object,
      default: () => ({}),
    },
    errors: {
      type: Array,
      default: () => [],
    },
    isLastChild: {
      type: Boolean,
      default: false,
    },
  },

  components: { KeyValuePairItems },

  mixins: [model, tabs, validation, size],

  data() {
    return {
      newData: null,
      updatePass: 0,
      keyValueArray: null,
      referencekeyValueArray: null,
      newKey: "",
      newValue: null,
    };
  },

  computed: {
    additionalProperties() {
      return this.schema.additionalProperties || {};
    },
  },

  methods: {
    initKeyValueArray() {
      this.keyValueArray = Object.keys(this.value).map((key) => ({
        key,
        value: this.value[key] || null,
      }));
    },
    initReferenceKeyValueArray() {
      this.referencekeyValueArray = Object.keys(this.referenceModel).map(
        (key) => ({
          key,
          value: this.referenceModel[key] || null,
        })
      );
    },

    updateKeyValueArray(value) {
      this.keyValueArray = Object.keys(value).map((key) => ({
        key,
        value: value[key],
      }));
    },

    reconstructObject(arr) {
      let result = {};
      arr.forEach((item) => {
        result = Object.assign(
          {},
          { ...result },
          {
            [`${item.key}`]: item.value,
          }
        );
      });
      return result;
    },

    async addProp() {
      const observerRef = `${this.schema.title.replace(/ /g, "-")}-new`;
      const isValid = await this.$refs[observerRef].validate();

      if (isValid) {
        this.keyValueArray.push({
          key: this.newKey,
          value: this.newValue,
        });

        this.newKey = "";
        this.newValue = null;

        this.updatePass += 1;
      }
    },

    deleteProp(index) {
      this.$delete(this.keyValueArray, index);
      this.updatePass += 1;
    },
  },

  watch: {
    keyValueArray: {
      immediate: true,
      deep: true,
      handler(newVal, oldVal) {
        if (oldVal !== null && oldVal !== undefined) {
          this.modelData = this.reconstructObject(newVal);
        }
      },
    },

    activeTab() {
      // re-calculate keyValueArray
      this.initKeyValueArray();
      this.initReferenceKeyValueArray();
    },

    value: {
      deep: true,
      immediate: true,
      handler(n, o) {
        const newStringifiedObject = JSON.stringify(n);
        const oldStringifiedObject = JSON.stringify(o);
        if (newStringifiedObject !== oldStringifiedObject)
          this.initKeyValueArray();

        this.initReferenceKeyValueArray();
      },
    },
  },
};
</script>
