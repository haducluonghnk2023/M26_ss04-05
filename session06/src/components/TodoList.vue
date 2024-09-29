<template>
  <div class="container">
    <div class="todo">
      <h3 class="heading">Danh sach cong viec</h3>
      <form @submit.prevent="handlAddJob" class="header">
        <input v-model="inputValue" type="text" class="input" />
        <button type="submit" class="button button-add">Add job</button>
      </form>

      <ul v-for="item in listJob" class="list-item" :key="item.id">
        <li class="item">
          <div class="left">
            <input
              @change="handleUpdateStatus"
              :checked="item.status"
              type="checkbox"
              :id="item.name"
            />
            <label
              :style="{ textDecoration: item.status ? 'line-through' : 'none' }"
              :for="item.name"
              >{{ item.name }}</label
            >
          </div>
          <div class="right">
            <button @click="handleDelete(item)" class="button button-delete">
              Delete
            </button>
          </div>
        </li>
      </ul>

      <footer class="footer">
        <p>Cong viec hoan thanh:</p>
        <p>
          <b>{{ completeJobs }}</b
          >/ <b>{{ listJob.length }}</b>
        </p>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, watchEffect } from "vue";
const inputValue = ref("");
const listJob = reactive(JSON.parse(localStorage.getItem("jobs") || "[]"));
const completeJobs = ref(0);

const saveData = (key, data) => {
  localStorage.setItem(key, JSON.stringify(data));
};
const handlAddJob = () => {
  // validate du lieu dau  vao
  if (!inputValue.value) {
    alert("Vui long nhap ten cong viec");
    return;
  }
  // kiem tra trung ten
  const findJobByName = listJob.find((item) => item.name === inputValue.value);
  if (findJobByName) {
    alert(`Ten cong viec ${inputValue.value} da ton tai`);
    return;
  }
  // them du lieu vao 1 mang
  listJob.push({
    id: Math.ceil(Math.random() * 10000000000000),
    name: inputValue.value,
    status: false,
  });
  // xoa input
  inputValue.value = "";
  // luu du lieu len local
  saveData("jobs", listJob);
};
const handleUpdateStatus = (id) => {
  const findIndex = listJob.findIndex((item) => item.id === id);
  listJob[findIndex].status = !listJob[findIndex].status;
  saveData("job", listJob);
};
const handleDelete = (item) => {
  const isConfirmDelete = confirm(
    `Bạn có xác nhận muốn xóa công việc ${item.name} không ? `
  );
  if (isConfirmDelete) {
    const filterJobs = listJob.filter((jobItem) => jobItem.id !== item.id);
    listJob.length = 0;
    listJob.push(...filterJobs);
    saveData("jobs", listJob);
  }
};
const updateStatusJob = () => {
  const filterJobCompletes = listJob.filter((item) => item.status);
  completeJobs.value = filterJobCompletes.length;
};

// watch(listJob, () => {
//   updateStatusJob();
// },{
//   deep:true,
//   immediate: true
// });

watchEffect(() => {
  updateStatusJob();
});
</script>

<style scoped>
.container {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.heading {
  text-align: center;
  font-size: 24px;
  padding-bottom: 24px;
  /* text-decoration: line-through; */
}
.todo {
  width: 600px;
  border: 1px solid #dadada;
  padding: 20px 24px;
}
</style>
