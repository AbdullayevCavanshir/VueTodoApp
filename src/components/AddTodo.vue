<template>
  <v-card elevation="1" class="pa-10 mx-12">
    <v-form>
      <v-text-field v-model="task.subject" label="Subject"></v-text-field>
      <div>
        Tags:
        <v-select
          v-model="selectedTags"
          :items="tagList"
          label="Select"
          multiple
          chips
        >
          <template v-slot:prepend-item>
            <v-list-item ripple @mousedown.prevent @click="toggle">
              <v-list-item-action>
                <v-icon
                  :color="selectedTags.length > 0 ? 'indigo darken-4' : ''"
                >
                  {{ icon }}
                </v-icon>
              </v-list-item-action>
              <v-list-item-content>
                <v-list-item-title> Select All </v-list-item-title>
              </v-list-item-content>
            </v-list-item>
            <v-divider class="mt-2"></v-divider>
          </template>
        </v-select>
        <span v-for="(tag, index) in task.tagList" :key="index">
          <v-chip
            close-icon="mdi-close"
            color="primary"
            close
            @click:close="deleteTag(index)"
            >{{ tag }}</v-chip
          >
          <span v-if="index + 1 != task.tagList.length" class="mx-1">,</span>
        </span>
      </div>
      <v-text-field
        v-model="tag"
        @keyup.enter="addTag"
        label="Tag"
      ></v-text-field>
      <v-btn rounded color="primary" class="mr-4" @click="addTask">
        Submit
      </v-btn>
      <v-btn rounded @click="clear"> Clear </v-btn>
    </v-form>
  </v-card>
</template>

<script>
export default {
  name: "AddTodo",
  data: () => ({
    task: {
      subject: null,
      tagList: [],
    },
    tag: null,
    selectedTags: [],
  }),
  props: {
    tagList: {
      type: Array,
    },
  },
  computed: {
    likesAllTags() {
      return this.selectedTags.length === this.tagList.length;
    },
    likesSomeTags() {
      return this.selectedTags.length > 0 ? true : false;
    },
    icon() {
      if (this.likesAllTags) return "mdi-close-box";
      if (this.likesSomeTags) return "mdi-minus-box";
      return "mdi-checkbox-blank-outline";
    },
  },
  methods: {
    addTag() {
      if (
        !this.task.tagList.includes(this.tag) &&
        !this.selectedTags.includes(this.tag)
      ) {
        this.task.tagList.push(this.tag);
        this.tag = null;
      }
    },
    deleteTag(index) {
      this.task.tagList.splice(index, 1);
    },
    addTask() {
      this.$emit("addTask", {
        ...this.task,
        tagList: [...this.task.tagList, ...this.selectedTags],
      });
      this.clear();
    },
    clear() {
      Object.assign(this.$data, this.$options.data());
    },
    toggle() {
      this.$nextTick(() => {
        if (this.likesAllTags) {
          this.selectedTags = [];
        } else {
          this.selectedTags = this.tagList.slice();
        }
      });
    },
  },
};
</script>
