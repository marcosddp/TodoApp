<template>
  <h1>#Todo</h1>
  <!--navbar-->
  <div id="navbar">
    <a href="#/All" @click="All"
      >All
      <div class="line-blue" :class="{ 'active-link': url == 'All' }"></div
    ></a>
    <a href="#/Active" @click="Active"
      >Active
      <div class="line-blue" :class="{ 'active-link': url == 'Active' }"></div
    ></a>
    <a href="#/Completed" @click="Completed"
      >Completed
      <div
        class="line-blue"
        :class="{ 'active-link': url == 'Completed' }"
      ></div
    ></a>
  </div>
  <!--navbar-->

  <!--form-->
  <div id="form" v-if="url != 'Completed'">
    <input id="content" placeholder="add details" v-model="content" />
    <button id="Add" v-on:click="addTask">Add</button>
  </div>
  <!--form-->

  <!--List-->

  <div class="list">
    <div v-for="task in filters[url](tasks)" :key="task.id">
      <label
        v-bind:style="
          task.completed
            ? 'text-decoration:line-through;color: #333333;'
            : task.completed
            ? 'display:none'
            : ''
        "
      >
        <input
          class="checkbox"
          type="checkbox"
          :checked="task.completed"
          v-on:click="check(task.id)"
        />
        <p>{{ task.content }}</p>
      </label>
      <button
        v-if="url == 'Completed'"
        class="icon"
        v-on:click="deleteTask(task.id)"
      >
        <font-awesome-icon icon="trash-alt" />
      </button>
    </div>
  </div>

  <!--List-->
</template>

<script>
import { setTransitionHooks } from "@vue/runtime-core";
var filters = {
  All: function (tasks) {
    return tasks;
  },
  Active: function (tasks) {
    return tasks.filter(function (tasks) {
      return !tasks.completed;
    });
  },
  Completed: function (tasks) {
    return tasks.filter(function (tasks) {
      return tasks.completed;
    });
  },
};

var getId = function (tasks, id) {
  return tasks.findIndex((e) => e.id === id);
};

var createId = function (tasks) {
  if (tasks.length == 0) return 0;

  return (
    Math.max.apply(
      Math,
      tasks.map(function (t) {
        return t.id;
      })
    ) + 1
  );
};

export default {
  data() {
    return {
      url: "Active",
      tasks: [],
      content: "",
      filters: filters,
    };
  },

  methods: {
    Active() {
      this.url = "Active";
    },
    All() {
      this.url = "All";
    },
    Completed() {
      this.url = "Completed";
    },

    addTask() {
      if (this.content) {
        this.tasks = JSON.parse(localStorage.getItem("tasks"));

        if (this.tasks == null) this.tasks = [];

        let task = {
          id: createId(this.tasks),
          completed: false,
          content: this.content,
        };

        localStorage.setItem("task", JSON.stringify(task));

        this.tasks.unshift(task);

        localStorage.setItem("tasks", JSON.stringify(this.tasks));

        this.content = "";
      }
    },

    check(id) {
      let i = getId(this.tasks, id);

      this.tasks[i].completed = !this.tasks[i].completed;
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },

    deleteTask(id) {
      let i = getId(this.tasks, id);
      this.tasks.splice(i, 1);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
  },

  mounted() {
    this.Active();
    window.location.href = "/#Active";

    this.tasks = JSON.parse(localStorage.getItem("tasks"));

    if (this.tasks == null) this.tasks = [];
  },
};
</script>

<style></style>
