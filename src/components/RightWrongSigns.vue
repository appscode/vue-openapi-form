<template>
  <div>
    <button class="button is-information is-success" v-if="valid">
      <i class="fa fa-check"></i>
    </button>
    <button class="button is-information is-warning" v-if="invalid">
      <i class="fa fa-times"></i>
    </button>
  </div>
</template>

<script>
export default {
  name: "right-wrong-signs",
  props: {
    valid: {
      type: Boolean,
      default: true,
    },
    invalid: {
      type: Boolean,
      default: true,
    },
  },
};
</script>
