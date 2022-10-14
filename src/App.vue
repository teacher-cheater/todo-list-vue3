<template>
  <div class="container">
    <div class="header">
      <h1 class="header__title">To do list</h1>
      <!-- ---- создание задачи ---- -->
      <button class="header__add-task" @click="showDialog">
        <span class="header__icon"></span>
      </button>
    </div>
    <!-- ---- поиск задач ---- -->
    <div class="search">
      <div class="search__content">
        <img src="./icons/search.svg" alt="search" />
        <!-- компонент поиска -->
        <search-for-tasks
          class="search__text"
          type="text"
          placeholder="Поиск Имени, статуса или даты"
          v-model="searchQuery"
        />
        <!--  -->
      </div>
      <!-- ----- сортировка дата/статус ---- -->
      <div class="search__select-block">
        <p class="search__sort">Сортировать по:</p>
        <!-- компонент выбора сортировки -->
        <my-select v-model="selectedSort" :options="sortOptions" />
      </div>

      <!--  -->
    </div>
    <div class="text">
      <p class="subtext">Описание</p>
      <div class="sort">
        <p class="subtext-status">Статус</p>
        <p class="subtext-date">Дата</p>
      </div>
    </div>
    <div class="tasks">
      <!-- --------- вывод задач ------------ -->
      <div class="task" v-for="task in sortedTasks">
        <div class="content">
          <!-- ---- checkbox ---- -->
          <label>
            <input
              type="checkbox"
              name="checkbox"
              class="checkbox"
              v-model="checked"
            />
            <span class="custom-checkbox"></span>
            <p class="problem">{{ task.title }}</p>
          </label>
          <!--  -->
        </div>
        <div class="block-status">
          <p class="status">{{ task.body }}</p>
          <p class="date">{{ task.date }}</p>
        </div>
      </div>
    </div>
    <!-- --------------- модальное окно ------------------- -->
    <div class="add-tasks-window" v-if:show="dialogVisible">
      <div class="create add-tasks-window">
        <div class="create__block">
          <div class="create__content">
            <h3 class="create__title">Создать новую задачу</h3>
            <button @click="closeModalWindow" class="create__close">
              <span class="create__close-icon"></span>
            </button>
          </div>
          <!-- ------------- форма заполнения модалки -------------- -->
          <form class="create__items" @submit.prevent>
            <p class="create__text">Описание</p>
            <input type="text" class="create__task" v-model="title" />
            <button class="create__btn" @click="addTask">Создать</button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SearchForTasks from "@/components/UI/SearchForTasks";
import MySelect from "@/components/UI/MySelect";

export default {
  components: { SearchForTasks, MySelect },
  data() {
    return {
      tasks: JSON.parse(localStorage.getItem("tasks")) ?? [],
      task: {
        title: "",
        body: "",
        date: "",
      },
      dialogVisible: false,
      searchQuery: "",
      selectedSort: "",
      sortOptions: [
        { value: "date", name: "дате" },
        { value: "title", name: "статусу" },
      ],
    };
  },
  computed: {
    sortedTasks() {
      if (!this.selectedSort) return this.filteredTasks;

      return this.filteredTasks.sort((task1, task2) => {
        return task1[this.selectedSort] - task2[this.selectedSort];
      });
    },
    filteredTasks() {
      if (!this.searchQuery) return [...this.tasks];
      return [...this.tasks].filter((post) =>
        post.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    addTask() {
      const newTask = {
        title: this.title,
        body: "В работе",
        date: new Date().toLocaleDateString(),
      };
      this.tasks.push(newTask);
      this.title = "";
    },
    closeModalWindow() {
      this.dialogVisible = false;
    },
    showDialog() {
      this.dialogVisible = true;
    },
  },
  watch: {
    tasks: {
      deep: true,
      handler(allTasks) {
        localStorage.setItem("tasks", JSON.stringify(allTasks));
      },
    },
  },
};
</script>

<style>
.container {
  max-width: 1330px;
  padding: 0 15px;
  margin: 0 auto;
  color: #16191d;
}
* {
  margin: 0;
  padding: 0;
  border: 0;
  box-sizing: border-box;
}

._open-window {
  display: block;
}

.header {
  display: flex;
  justify-content: space-between;
  padding: 10px 40px;
}

.header__title {
  font-weight: 700;
  font-size: 24px;
  line-height: 132%;
  color: #16191d;
}

.header__add-task {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #d6dbeb;
  position: relative;
  cursor: pointer;
}

.header__icon {
  background: #314b99;
  width: 20px;
  height: 2px;
  position: absolute;
  top: 18px;
  left: 10px;
}

.header__icon::before {
  content: "";
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  width: 20px;
  height: 2px;
  background: #314b99;
  transform: rotate(90deg);
}
/* ------ стили для поиска------- */
.search {
  padding: 10px 40px 10px 20px;
  display: flex;
  justify-content: space-between;
}

.search__content {
  display: flex;
  gap: 16px;
}

input:active,
:hover,
:focus {
  outline: 0;
  outline-offset: 0;
}

.search__text {
  width: 100%;
  min-width: 300px;
  font-weight: 400;
  font-size: 14px;
  line-height: 132%;
  color: #c4c4c4;
}

.text {
  display: flex;
  justify-content: space-between;
  padding: 10px 40px 10px 60px;
}
.subtext-status,
.subtext-date,
.subtext {
  font-weight: 400;
  font-size: 14px;
  line-height: 132%;
  color: #16191d;
  border-left: 1px solid #c4c4c4;
  padding: 0 0 0 20px;
  width: 150px;
}

.sort {
  display: flex;
}
.search__select-block {
  display: flex;
  gap: 10px;
}
/* ------ блок с задачами -------- */
.task {
  display: flex;
  justify-content: space-between;
  padding: 20px 40px 20px 20px;
  border-bottom: 1px solid #c4c4c4;
  border-top: 1px solid #c4c4c4;
}

.block-status {
  display: flex;
}

.content {
  display: flex;
  justify-content: space-between;
}
/* --------------checkbox--------------- */
label {
  display: flex;
}

.checkbox {
  width: 0;
  height: 0;
  opacity: 0;
  position: absolute;
  z-index: -1;
}

.custom-checkbox {
  position: relative;
  display: inline-block;
  width: 20px;
  height: 20px;
  vertical-align: sub;
  cursor: pointer;
  background: url("./icons/circle.svg") 0 0 / contain no-repeat;
}

.custom-checkbox::before {
  content: "";
  display: inline-block;
  width: 28px;
  height: 28px;
  background: url("./icons/checked.svg") 0 0 / contain no-repeat;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) scale(0);
  transition: 0.1s ease 0s;
  margin-top: 3.8px;
}
.checkbox:checked + .custom-checkbox::before {
  transform: translate(-50%, -50%) scale(1);
}

/*--------------------*/
.status {
  font-weight: 400;
  font-size: 14px;
  line-height: 132%;
  padding: 0 0 0 20px;
  width: 150px;
  color: #f89b11;
}
/*.status-check{
      font-weight: 400;
      font-size: 14px;
      line-height: 132%;
      padding: 0 0 0 20px;
      width: 150px;
          color: #134EC1;
    }*/
/* --------------------- */
.problem {
  font-weight: 400;
  font-size: 14px;
  line-height: 132%;
  color: #16191d;
  padding: 0 20px;
  cursor: pointer;
  margin-left: 20px;
}

.date {
  font-weight: 400;
  font-size: 14px;
  line-height: 132%;
  color: #16191d;
  padding: 0 0 0 20px;
  width: 150px;
}
.tasks-window {
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(255, 255, 255, 0.01);
  backdrop-filter: blur(4px);
  position: fixed;
  display: flex;
}
.tasks-window__content {
  margin: auto;
  padding: 40px 40px 50px 40px;
  background: #ffffff;
  border: 1px solid #dde2e4;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  border-radius: 6px;
  min-width: 400px;
}
.create {
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(255, 255, 255, 0.01);
  backdrop-filter: blur(4px);
  position: fixed;
  display: flex;
}

.create__block {
  margin: auto;
  padding: 40px 40px 50px 40px;
  background: #ffffff;
  border: 1px solid #dde2e4;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  border-radius: 6px;
  min-width: 400px;
}

.create__content {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30px;
  width: 400px;
}

.create__title {
  font-weight: 700;
  font-size: 18px;
  line-height: 132%;
  color: #16191d;
}
.create__close {
  background: #314b99;
  border-radius: 5px;
  width: 30px;
  height: 30px;
  position: relative;
  cursor: pointer;
}
.create__close-icon {
  background: #ffffff;
  width: 18px;
  height: 2px;
  position: absolute;
  top: 14px;
  left: 6px;
  transform: rotate(45deg);
}
.create__close-icon::before {
  content: "";
  transform: rotate(90deg);
  background: #ffffff;
  width: 18px;
  height: 2px;
  position: absolute;
  top: 0;
  left: 0;
}

.create__text {
  font-weight: 400;
  font-size: 14px;
  line-height: 14px;
  color: #16191d;
  margin-bottom: 5px;
}
.create__task {
  background: #ffffff;
  border: 1px solid #dde2e4;
  border-radius: 8px;
  width: 100%;
  padding: 16px 11px;
  color: #000000;
  opacity: 0.5;
  margin-bottom: 30px;
}
.create__btn {
  display: flex;
  background: #f0f5ff;
  border-radius: 8px;
  padding: 12px 40px;
  font-weight: 400;
  font-size: 18px;
  line-height: 132%;
  color: #314b99;
  margin: 0 auto;
  cursor: pointer;
}
</style>
