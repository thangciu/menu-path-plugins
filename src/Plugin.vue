<template>
  <form>
    <div v-for="data in datas" :key="data" class="form-check">
      <input
        @change="onSelected(data.path, data.entityId)"
        :checked="model.id == data.entityId"
        type="checkbox"
        class="form-check-input"
        id="anime"
        name="hobby"
      />
      <label class="form-check-label" for="anime">{{ data.path || '/' }}</label>
      <div
        v-for="child_lv1 in data.children"
        :key="child_lv1"
        class="form-check ml-4"
      >
        <input
          @change="onSelected(child_lv1.path, child_lv1.entityId)"
          :checked="model.id == child_lv1.entityId"
          type="checkbox"
          class="form-check-input"
          id="anime"
          name="hobby"
        />
        <label class="form-check-label" for="anime">{{ child_lv1.path || '/' }}</label>
        <div
          v-for="child_lv2 in child_lv1.children"
          :key="child_lv2"
          class="form-check ml-4"
        >
          <input
            @change="onSelected(child_lv2.path, child_lv2.entityId)"
            :checked="model.id == child_lv2.entityId"
            type="checkbox"
            class="form-check-input"
            id="anime"
            name="hobby"
          />
          <label class="form-check-label" for="anime">{{
            child_lv2.path || '/'
          }}</label>
        </div>
      </div>
    </div>
  </form>
</template>

<script>
import gpl from "graphql-tag";
import { GraphQLClient } from "graphql-request";
export default {
  mixins: [window.Storyblok.plugin],
  data() {
    return {
      datas: {},
    };
  },
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "menu-path",
        value: null,
        id: null
      };
    },
    pluginCreated() {
      const endpoint = this.options.endpoint;
      const token = this.options.token;
      const query = gpl`
        query {
          site{
            categoryTree{
              path
              name
              entityId
              children{
                name
                path
                entityId
                children{
                  name
                  path
                  entityId
                }
              }
            }
          }
          }
          `;
      const graphQLClient = new GraphQLClient(endpoint, {
        headers: {
          authorization: `Bearer ${token}`,
        },
      });

      const loadData = async () => {
        return await graphQLClient.request(query);
      };
      loadData().then((res) => {
        this.datas = res.site.categoryTree;
      });
    },
    onSelected(value, id) {
     this.$emit('changed-model', {
       ...this.model,
       "value": value,
       "id": id
     });
    }
  },
  components: {},
};
</script>

<style lang="scss" scoped>
.form-check-label {
  margin-left: 10px;
}
.form-check {
  margin-top: 10px;
}
.ml-4 {
  margin-left: 1rem;
}
</style>

