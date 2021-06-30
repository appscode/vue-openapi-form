<template>
  <div class="form-left-item mb-15">
    <template v-if="items.type === 'object'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleObject(true)"
        :name="`${schema.title.replace(/ /g, '-')}-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-${index + 1}-provider`"
        slim
      >
        <object-form-wrapper
          :schema="{
            ...items,
            ...{ title: `${schema.title} ${index + 1}` },
          }"
          :isSelfRequired="true"
          :type="items.type"
          :errors="errors"
          v-model="modelData[index]"
          :reference-model="referenceModel[index] || {}"
        />
      </validation-provider>
    </template>
    <template v-else-if="items.type === 'key-value-pairs'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleObject(true)"
        :name="`${schema.title.replace(/ /g, '-')}-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-${index + 1}-provider`"
        slim
      >
        <key-value-pairs
          :errors="errors"
          :schema="{
            ...items,
            ...{ title: `${schema.title} ${index + 1}` },
          }"
          :type="items.type"
          v-model="modelData[index]"
          :reference-model="referenceModel[index] || {}"
        />
      </validation-provider>
    </template>
    <template v-else-if="items.type === 'array'">
      <validation-provider
        v-slot="{ errors }"
        :rules="ruleArray(true)"
        :name="`${schema.title.replace(/ /g, '-')}-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-${index + 1}-provider`"
        slim
      >
        <array-input
          :schema="{
            ...items,
            ...{ title: `${schema.title} ${index + 1}` },
          }"
          :type="items.type"
          :errors="errors"
          v-model="modelData[index]"
          :reference-model="referenceModel[index] || []"
        />
      </validation-provider>
    </template>
    <template v-else>
      <validation-provider
        v-slot="validationOb"
        :rules="ruleString(true)"
        :name="`${schema.title.replace(/ /g, '-')}-${index + 1}`"
        :vid="`${schema.title.replace(/ /g, '-')}-${index + 1}-provider`"
        slim
      >
        <simple-input
          :schema="{
            ...items,
            ...{ title: `${schema.title} ${index + 1}` },
          }"
          :type="items.type"
          :required="true"
          :validationOb="validationOb"
          v-model="modelData[index]"
          :reference-model="referenceModel[index] || ''"
        />
      </validation-provider>
    </template>
  </div>
</template>

<script>
import validation from "../../mixins/validation.js";
import { model } from "../../mixins/model.js";

export default {
  name: "array-input-items",
  props: {
    items: {
      type: Object,
      default: () => ({}),
    },
    schema: {
      type: Object,
      default: () => ({}),
    },
    index: {
      type: Number,
      default: 0,
    },
    value: {
      type: null,
      default: () => [],
    },
  },
  mixins: [model, validation],
};
</script>
