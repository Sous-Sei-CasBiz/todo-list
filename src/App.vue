<template>
  <div
    class="
      p-[40px]
      max-h-[60%]
      [&::-webkit-scrollbar]:hidden
      max-w-[80%]
      md:w-[70%]
      lg:w-[50%]
      overflow-auto
      bg-slate-100
      shadow-md shadow-slate-600
      rounded-md
    "
  >
    <div class="text-center">
      <h1 class="title text-2xl font-display">Your Day</h1>
    </div>

    <!-- =====FORM==== -->
    <div class="mt-6">
        <div
          class="
            border
            rounded-md
            px-4
            py-2
            bg-white
            flex
            items-center
            justify-between
            gap-2
          "
        >
          <input
            type="text"
            v-model="name"
            class="px-4 py-2 rounded-md outline-none w-full"
            placeholder="Add your task here..."
          />
          <button
            @click="addTodo()"
            title="Add Todo"
            class="
              py-1
              px-2
              border-2
              border-slate-200
              rounded-md
              hover:bg-lime-700 hover:text-white hover:border-lime-700
            "
          >
            <i class="ri-add-circle-line"></i>
          </button>
          <button
            @click="searchTodo"
            title="Search Todo"
            type="button"
            class="
              py-1
              px-2
              border-2
              border-slate-200
              rounded-md
              hover:bg-lime-700 hover:text-white hover:border-lime-700
            "
          >
            <i class="ri-find-replace-line"></i>
          </button>
        </div>
   
    </div>
    <!-- <div class="flex items-center">
      <div class="w-full h-[2px] bg-black my-10"></div>
      <h1 class="whitespace-nowrap text-2xl">Your Todo Here</h1>
      <div class="w-full h-[2px] bg-black my-10"></div>
    </div> -->

    <div class="mt-4">
      <div
        v-for="todoList in todos"
        :key="todoList.id"
        class="
          px-4
          py-2
          border border-b-slate-400
          hover:bg-white
          rounded-md
          mt-1
          cursor-pointer
        "
      >
        <div v-if="todoList.edit">
      
            <div class="flex justify-between whitespace-normal">
              <input
                type="text"
                v-model="editText"
                class="w-full bg-white outline-none border pl-4 mr-4 rounded-md"
              />
              <div>
                <button
                  class="bg-blue-600 rounded-md px-2 py-1 text-white"
                  @click="saveTodo(todoList.id)"
                >
                  <span>Save</span>
                </button>
              </div>
            </div>
        </div>
        <div v-else class="flex justify-between">
          <span v-if="todoList.done">
            <button @click="doneTodo(todoList.id)">
              <span v-show="todoList.done" title="Undo">
                <i
                  class="
                    ri-check-double-line
                    mr-2
                    text-white
                    rounded-full
                    p-1
                    border
                    bg-lime-600
                  "
                ></i>
              </span>
            </button>
            <s class="text-red-700" title="Done">{{ todoList.todo }}</s>
          </span>

          <span v-else>
            {{ todoList.todo }}
          </span>

          <!-- ================ -->

          <div class="flex gap-2">
            <button @click="doneTodo(todoList.id)">
              <span v-if="!todoList.done" title="Make it Done">
                <i
                  class="
                    ri-check-line
                    text-lime-700
                    hover:text-white
                    rounded-md
                    p-1
                    border-2
                    hover:border-lime-600 hover:bg-lime-600
                  "
                ></i>
              </span>
            </button>
            <button  @click="editTodo(todoList.id)">
              <span v-if="!todoList.done" title="Edit to do">
                <i
                  class="
                    ri-file-edit-line
                    text-blue-700
                    hover:text-white
                    rounded-md
                    p-1
                    border-2
                    hover:border-blue-600 hover:bg-blue-600
                  "
                ></i>
              </span>
            </button>
            <button @click="removeTodo(todoList)" title="Delete">
              <i
                class="
                  ri-delete-bin-6-line
                  hover:text-white
                  text-red-600
                  rounded-md
                  p-1
                  border-2
                  hover:border-red-600 hover:bg-red-600
                "
              ></i>
            </button>
          </div>
        </div>

        <!-- ============ -->
      </div>
    </div>
  </div>
  <!-- ============== -->
</template>

<script >
export default {
  data() {
    return {
      edit: false,
      name: "",
      editText: "",
      search: "",
      todos: [],
    };
  },

  mounted() {

    this.todos = JSON.parse(localStorage.getItem("todos")) || [];
    //check local storage have data
    if (this.todos.length == 0) {
      fetch("https://dummyjson.com/todos")
        .then((res) => res.json())
        .then((response) => {
          this.todos = response.todos;
          localStorage.setItem("todos", JSON.stringify(this.todos));

        });
    }
  },

  methods: {
    // =================
    searchTodo() {
      this.todos = JSON.parse(localStorage.getItem("todos")) || [];
      this.todos = this.todos.filter((todos) =>
        todos.todo.toLowerCase().includes(this.name.toLowerCase())
      );
    },

    // ==============
    addTodo() {
      this.todos = JSON.parse(localStorage.getItem("todos")) || [];
      var index = this.todos != null ? this.todos.length + 1 : 1;
      //object store data
      var obj = {
        id: index,
        todo: this.name,
        done: false,
      };
      //add obj to todo list
      this.todos.unshift(obj);
      localStorage.setItem("todos", JSON.stringify(this.todos));
      
    },
    // ===================
    removeTodo(todoList) {
      this.todos.splice(this.todos.indexOf(todoList), 1);
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },
    // ================
    doneTodo(id) {
      this.todos.forEach((todoList) => {
        if (id == todoList.id) {
          todoList.done = !todoList.done;
        }
      });
      this.todos.filter((todoList) => todoList.done != false);
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },
    // =====================
    editTodo(id) {
      this.todos.forEach((todoList) => {
        if (id == todoList.id) {
          todoList.edit = true;
          this.editText = todoList.todo;
        } else {
          todoList.edit = false;
        }
      });
    },
    // ===================
    saveTodo(id) {
      this.todos.forEach((todoList) => {
        if (id == todoList.id) {
          todoList.edit = false;
          todoList.todo = this.editText;
        }
      });
      this.edit = false;
      localStorage.setItem("todos", JSON.stringify(this.todos));

    },
  },
};
</script>
