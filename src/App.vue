<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card elevation="10" class="pa-10">
          <v-card-title class="d-flex justify-center mb-5">
            VueTodoApp
          </v-card-title>

          <v-row>
            <v-col class="pt-5">
              <v-btn
                elevation="2"
                rounded
                class=""
                @click="isShowAddSection = !isShowAddSection"
              >
                <v-icon color="indigo darken-4">
                  {{ !isShowAddSection ? "mdi-plus" : "mdi-close" }}
                </v-icon>
                {{ isShowAddSection ? "close" : "add task" }}</v-btn
              >
            </v-col>
            <v-col>
              <v-select
                v-model="selectedTags"
                :items="tagList"
                label="Tags"
                multiple
                chips
                solo
              >
                <template v-slot:prepend-item>
                  <v-list-item ripple @mousedown.prevent @click="toggle">
                    <v-list-item-action>
                      <v-icon
                        :color="
                          selectedTags.length > 0 ? 'indigo darken-4' : ''
                        "
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
            </v-col>
          </v-row>

          <add-todo
            v-show="isShowAddSection"
            :tagList="tagList"
            :icon="icon"
            :likesSomeTags="likesSomeTags"
            :likesAllTags="likesAllTags"
            @addTask="(task) => addTask(task)"
          />
          <todo-list
            :taskList="getFilteredTaskList"
            :tagList="tagList"
            @deleteTask="(subject) => deleteTask(subject)"
            @deleteTag="(val) => deleteTag(val)"
            @addTag="(val) => addTag(val)"
            @selectTag="(val) => selectTag(val)"
            class="mt-4"
          />
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import AddTodo from "./components/AddTodo";
import TodoList from "./components/TodoList";
export default {
  name: "App",

  components: {
    AddTodo,
    TodoList,
  },

  data: () => ({
    taskList: [],
    tagList: [],
    selectedTags: [],
    isShowAddSection: false,
  }),
  computed: {
    getFilteredTaskList() {
      let filteredTaskList = this.taskList.filter((task) =>
        task.tagList.some((tag) => this.selectedTags.includes(tag))
      );
      return filteredTaskList.length ? filteredTaskList : this.taskList;
    },
    likesAllTags() {
      return this.selectedTags.length === this.tagList.length;
    },
    likesSomeTags() {
      return this.selectedTags.length > 0;
    },
    icon() {
      if (this.likesAllTags) return "mdi-close-box";
      if (this.likesSomeTags) return "mdi-minus-box";
      return "mdi-checkbox-blank-outline";
    },
  },
  watch: {
    taskList: {
      deep: true,
      handler() {
        localStorage.setItem("taskList", JSON.stringify(this.taskList));
      },
    },
  },
  methods: {
    addTask(taskData) {
      let task = {
        ...taskData,
        isCompleted: false,
        createdDate: `${new Date().toISOString().split("T")[0]} ${new Date()
          .toISOString()
          .split("T")[1]
          .slice(0, 8)}`,
      };
      this.tagList = [...new Set([...this.tagList, ...taskData.tagList])];
      this.taskList = [...this.taskList, task];
      localStorage.setItem("taskList", JSON.stringify(this.taskList));
      localStorage.setItem("tagList", JSON.stringify(this.tagList));
    },
    deleteTask(subject) {
      let task = this.taskList.find((task) => task.subject == subject);
      let taskIndex = this.taskList.indexOf(task);
      this.taskList.splice(taskIndex, 1);
      localStorage.setItem("taskList", JSON.stringify(this.taskList));
    },
    deleteTag(val) {
      this.taskList[val.taskIndex].tagList.splice(val.tagIndex, 1);
      localStorage.setItem("taskList", JSON.stringify(this.taskList));
    },
    addTag(val) {
      this.taskList[val.taskIndex].tagList.push(val.tag);
      this.tagList = [...new Set([...this.tagList, val.tag])];

      localStorage.setItem("taskList", JSON.stringify(this.taskList));
      localStorage.setItem("tagList", JSON.stringify(this.tagList));
    },
    selectTag(val) {
      this.taskList[val.taskIndex].tagList = [
        ...this.taskList[val.taskIndex].tagList,
        ...val.selectedTags,
      ];
      this.tagList = [...new Set([...this.tagList, ...val.selectedTags])];

      localStorage.setItem("taskList", JSON.stringify(this.taskList));
      localStorage.setItem("tagList", JSON.stringify(this.tagList));
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
  created() {
    if (localStorage.getItem("taskList")) {
      this.taskList = JSON.parse(localStorage.getItem("taskList"));
    }
    if (localStorage.getItem("tagList")) {
      this.tagList = JSON.parse(localStorage.getItem("tagList"));
    }
  },
};
</script>
