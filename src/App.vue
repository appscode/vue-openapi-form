<template>
  <div id="app">
    <div class="ac-navbar-area is-fixed" id="header">
      <div class="ac-navbar-inner">
        <!-- navbar start  -->
        <nav class="ac-navbar">
          <!-- navbar logo start  -->
          <div class="ac-navbar-brand">
            <a href="https://byte.builders/"
              ><img
                class="desktop-only"
                src="@/assets/images/bytebuilders-logo-white.svg"
              />

              <img
                class="responsive-only"
                src="https://cdn.appscode.com/images/products/bytebuilders/bytebuilders.png"
                alt=""
              />
            </a>
          </div>
          <!-- navbar logo end -->
          <p class="pl-20" style="color: white">(Vue OpenAPI Form)</p>
          <!-- navbar menus start  -->
          <div class="ac-navbar-menu">
            <!-- single navbar menu item start  -->
            <div class="ac-menu-item">
              <div class="social-link">
                <a
                  href="https://github.com/appscode/vue-openapi-form"
                  class="icon-rounded"
                >
                  <i class="fa fa-github" aria-hidden="true"></i>
                </a>
              </div>
            </div>
            <!-- single navbar menu item end  -->
          </div>
          <!-- navbar menus end -->
        </nav>
        <!-- navbar end -->
      </div>
    </div>

    <div class="vue-form-scema-body mb-50 mt-50">
      <div class="is-flex">
        <div class="left-content">
          <div class="left-content-wrapper">
            <div class="select-box-wrapper" v-if="!modifiedSchema">
              <label class="mb-10 is-block" for="schema-selection"
                >Select Schema</label
              >
              <div class="select is-fullwidth">
                <select id="schema-selection" v-model="selectedJsonSchema">
                  <option
                    v-for="jsonSchema in jsonSchemas"
                    :key="jsonSchema.title"
                    :value="jsonSchema"
                  >
                    {{ jsonSchema.title }}
                  </option>
                </select>
              </div>
            </div>
            <div class="level" v-else>
              <div class="level-left">Schema has been modified</div>
              <div class="level-right">
                <button class="button is-warning" @click.prevent="resetForm()">
                  Reset
                </button>
              </div>
            </div>
            <schema-model
              :key="JSON.stringify(selectedJsonSchema)"
              :schemaModel="selectedJsonSchema"
              @submit="updateSchema"
            />
          </div>
        </div>
        <div class="right-content">
          <vue-openapi-form
            :schema="jsonSchema"
            v-model="model"
            :reference-model="referenceModel || ''"
            :formTitle="formTitle"
            :key="JSON.stringify(selectedJsonSchema)"
            :onValid="onValid"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueOpenapiForm from "@/components/VueOpenapiForm";
import Schemas from "@/json-schema";
import SchemaModel from "@/components/SchemaModel";

export default {
  name: "app",
  components: {
    VueOpenapiForm,
    SchemaModel,
  },

  data() {
    return {
      jsonSchemas: Schemas,
      selectedJsonSchema: Schemas[0],
      jsonSchema: {},
      model: {},
      referenceModel: {},
      formTitle: "",
      modifiedSchema: false,
    };
  },

  methods: {
    onValid() {
      // console.log("Form is Valid");
      // console.log(this.model);
    },
    updateSchema(e) {
      this.modifiedSchema = true;
      this.selectedJsonSchema = e;
    },
    resetForm() {
      this.modifiedSchema = false;
      this.selectedJsonSchema = Schemas[0];
    },
  },

  watch: {
    selectedJsonSchema: {
      deep: true,
      immediate: true,
      async handler(newVal) {
        this.jsonSchema = JSON.parse(JSON.stringify(newVal.schema));
        await setTimeout(() => {
          this.model = JSON.parse(JSON.stringify(newVal.model));
          this.referenceModel = JSON.parse(JSON.stringify(this.model));
        }, 2000);
        this.formTitle = newVal.title;
      },
    },
  },
};
</script>
