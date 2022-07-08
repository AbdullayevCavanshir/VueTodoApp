<template>
  <v-expansion-panels class="" v-model="panel">
    <v-expansion-panel v-for="(task, taskIndex) in taskList" :key="taskIndex">
      <v-expansion-panel-header @click="showAndHidePanel(taskIndex)">
        <div class="d-flex flex-column">
          <h3 :class="task.isCompleted ? 'text-decoration-line-through' : ''">
            {{ task.subject }}
          </h3>
          <span class="mt-2">{{ task.createdDate }}</span>
          <div class="d-flex mt-3" v-if="taskIndex != openPanelIndex">
            <span>
              <v-chip small :color="task.isCompleted ? 'primary' : 'error'">{{
                task.isCompleted ? "Completed" : "Not completed"
              }}</v-chip>
              <span class="mx-1">,</span>
            </span>
            <span v-for="(tag, tagIndex) in task.tagList" :key="tagIndex">
              <v-chip
                small
                color="cyan"
                class="white--text"
                @click:close="deleteTag(tagIndex, taskIndex)"
                >{{ tag }}</v-chip
              >
              <span v-if="tagIndex + 1 != task.tagList.length" class="mx-1"
                >,</span
              >
            </span>
          </div>
        </div>
      </v-expansion-panel-header>
      <v-expansion-panel-content>
        <v-form>
          <v-text-field v-model="task.subject" label="Subject"></v-text-field>
          <div>
            Tags:
            <v-select
              v-model="selectedTags"
              :items="tagList"
              @change="selectTag(taskIndex)"
              label="Select"
              multiple
              chips
              v-if="tagList.length"
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
            <span v-for="(tag, tagIndex) in task.tagList" :key="tagIndex">
              <v-chip
                close-icon="mdi-close"
                color="primary"
                close
                @click:close="deleteTag(tagIndex, taskIndex)"
                >{{ tag }}</v-chip
              >
              <span v-if="tagIndex + 1 != task.tagList.length" class="mx-1"
                >,</span
              >
            </span>
          </div>
          <v-text-field
            v-model="tag"
            @keyup.enter="addTag(taskIndex)"
            label="Tag"
          ></v-text-field>
          <div class="d-flex">
            <span>Completed</span>
            <v-checkbox
              v-model="task.isCompleted"
              class="mt-0 ml-3"
            ></v-checkbox>
          </div>
          <v-btn rounded @click="deleteTask(task.subject)" color="error">
            delete
          </v-btn>
        </v-form>
      </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
</template>

<script>
export default {
  name: "TodoList",
  data: () => ({
    panel: [],
    selectedTags: [],
    openPanelIndex: null,
    tag: null,
  }),
  props: {
    taskList: {
      type: Array,
    },
    tagList: {
      type: Array,
    },
  },
  methods: {
    showAndHidePanel(taskIndex) {
      this.openPanelIndex = taskIndex != this.openPanelIndex ? taskIndex : null;
    },
    deleteTask(subject) {
      this.$emit("deleteTask", subject);
    },
    addTag(taskIndex) {
      if (!this.taskList[taskIndex].tagList.includes(this.tag)) {
        this.$emit("addTag", { taskIndex, tag: this.tag });
        this.tag = null;
      }
    },
    selectTag(taskIndex) {
      this.$emit("selectTag", { taskIndex, selectedTags: this.selectedTags });
    },
    deleteTag(tagIndex, taskIndex) {
      this.$emit("deleteTag", { tagIndex, taskIndex });
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
};
</script>
