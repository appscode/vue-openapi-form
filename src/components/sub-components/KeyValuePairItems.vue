<template>
  <div class="key-value-save">
    <validation-provider
      :vid="`${schema.title.replace(/ /g, '-')}-key-${index + 1}-provider`"
      rules="required"
      :name="`${schema.title.replace(/ /g, '-')}-key-${index + 1}`"
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
        v-model="modelData.key"
        :reference-model="referenceModel.key || ''"
      />
    </validation-provider>
    <template v-if="additionalProperties.type === 'object'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleObject(true)"
        :name="`${schema.title.replace(/ /g, '-')}-value-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-value-${index + 1}-provider`"
        slim
      >
        <object-form-wrapper
          :schema="additionalProperties"
          :isSelfRequired="true"
          :type="additionalProperties.type"
          :errors="errors"
          v-model="modelData.value"
          :reference-model="referenceModel.value || {}"
        />
      </validation-provider>
    </template>
    <template v-else-if="additionalProperties.type === 'key-value-pairs'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleObject(true)"
        :name="`${schema.title.replace(/ /g, '-')}-value-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-value-${index + 1}-provider`"
        slim
      >
        <key-value-pairs
          :schema="additionalProperties"
          :type="additionalProperties.type"
          :errors="errors"
          v-model="modelData.value"
          :reference-model="referenceModel.value || {}"
        />
      </validation-provider>
    </template>
    <template v-else-if="additionalProperties.type === 'array'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleArray(true)"
        :name="`${schema.title.replace(/ /g, '-')}-value-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-value-${index + 1}-provider`"
        slim
      >
        <array-input
          :schema="additionalProperties"
          :type="additionalProperties.type"
          :errors="errors"
          v-model="modelData.value"
          :reference-model="referenceModel.value || []"
        />
      </validation-provider>
    </template>
    <template v-else>
      <validation-provider
        v-slot="validationOb"
        :rules="ruleString(true)"
        :name="`${schema.title.replace(/ /g, '-')}-value-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-value-${index + 1}-provider`"
        slim
      >
        <simple-input
          :schema="additionalProperties"
          :type="additionalProperties.type"
          :validationOb="validationOb"
          v-model="modelData.value"
          :reference-model="referenceModel.value || ''"
        />
      </validation-provider>
    </template>
    <button
      class="button ac-button is-small is-square is-outlined-gray is-transparent"
      @click.prevent="deleteProp(index)"
    >
      <span class="icon is-small">
        <i class="fa fa-trash"></i>
      </span>
    </button>
  </div>
</template>

<script>
import validation from "../../mixins/validation.js";
import { model } from "../../mixins/model.js";

export default {
  name: "key-value-pair-items",
  props: {
    value: {
      type: Object,
      default: () => ({}),
    },
    index: { type: Number, default: 0 },
    schema: {
      type: Object,
      default: () => ({}),
    },
    additionalProperties: {
      type: Object,
      default: () => ({}),
    },
  },
  mixins: [model, validation],
};
</script>
