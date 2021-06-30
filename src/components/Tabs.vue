<template>
  <div class="tabs ac-tabs is-line">
    <ul>
      <li :class="{ 'is-active': activeTab === 'form' }">
        <a @click.prevent="showForm()">
          <span class="icon is-small"
            ><i
              class="fa fa-file-text fa-flip-horizontal"
              aria-hidden="true"
            ></i
          ></span>
          <span>Form</span>
        </a>
      </li>
      <li :class="{ 'is-active': activeTab === 'yaml' }">
        <a @click.prevent="showYaml()">
          <span class="icon is-small"
            ><i class="fa fa-code" aria-hidden="true"></i
          ></span>
          <span>YAML</span>
        </a>
      </li>
      <li :class="{ 'is-active': activeTab === 'json' }">
        <a @click.prevent="showJson()">
          <span class="icon is-small"
            ><i class="fa fa-code" aria-hidden="true"></i
          ></span>
          <span>JSON</span>
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "tabs",
  data() {
    return {
      activeTab: "form",
    };
  },

  methods: {
    showForm() {
      this.activeTab = "form";
      this.$emit("input", "form");
    },
    showJson() {
      this.activeTab = "json";
      this.$emit("input", "json");
    },
    showYaml() {
      this.activeTab = "yaml";
      this.$emit("input", "yaml");
    },
  },
};
</script>
