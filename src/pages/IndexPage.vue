<template>
  <div class="q-pa-md" style="max-width: 300px">
    <form
      @submit.prevent.stop="onSubmit()"
      @reset.prevent.stop="onReset"
      class="q-gutter-md"
    >
      <q-input
        ref="nameRef"
        filled
        v-model="name"
        label="您的姓名*"
        lazy-rules
        :rules="nameRules"
      />
      <q-input
        ref="ageRef"
        filled
        type="number"
        v-model="age"
        label="您的年齡*"
        lazy-rules
        :rules="ageRules"
        @keypress="validateKeyPress"
      />
      <div>
        <q-btn
          :label="isEditing ? '編輯' : '新增'"
          type="submit"
          color="primary"
        />
        <q-btn label="清除" type="reset" color="primary" flat class="q-ml-sm" />
      </div>
    </form>

    <!-- table -->

    <q-table title="" :rows="rows" :columns="columns" row-key="name">
      <template v-slot:body-cell-icon="props">
        <q-td :props="props" class="iconWrapper">
          <q-icon
            class="icon editIcon"
            name="fa-regular fa-pen-to-square"
            size="lg"
            @click="() => handleEdit($event, props)"
          />
          <q-icon
            class="icon deleteIcon"
            name="fa-solid fa-trash"
            @click="alert($event, props)"
          ></q-icon>
        </q-td>
      </template>
    </q-table>
  </div>
</template>

<script setup>
import { useQuasar } from "quasar";
import { ref } from "vue";

const $q = useQuasar();

const name = ref(null);
const nameRef = ref(null);

const age = ref(null);
const ageRef = ref(null);

const rows = ref([]);

const editedItemId = ref(null);

const isEditing = ref(false);

const nameRules = [(val) => (val && val.length > 0) || "請勿空白"];

const ageRules = [
  (val) => (val !== null && val !== "") || "請輸入您的年齡",
  (val) => (val > 0 && val < 100) || "請輸入您真實的年齡",
];

const columns = [
  {
    name: "name",
    required: true,
    label: "姓名",
    align: "left",
    field: "name",
    format: (val) => `${val}`,
    sortable: true,
  },
  {
    name: "age",
    align: "left",
    label: "年齡",
    field: "age",
    sortable: true,
  },
  {
    name: "icon",
    align: "left",
    label: "",
    field: "icon",
    sortable: false,
    format: (val) => `${val}`,
  },
];

const validateKeyPress = (event) => {
  const char = String.fromCharCode(event.keyCode);
  const isDigit = /^\d+$/.test(char);
  if (!isDigit) {
    event.preventDefault();
  }
};

const onSubmit = () => {
  nameRef.value.validate();
  ageRef.value.validate();

  if (nameRef.value.hasError || ageRef.value.hasError) {
    $q.notify({
      icon: "error",
      color: "negative",
      message: "請確認您輸入的資料",
    });
  } else {
    // switch isEditing ?

    switch (true) {
      case isEditing.value:
        // 下面這裡按下按鈕之後 前面是更新後的值，該怎麼重新賦值到該row內?

        // console.log(name.value, Object.values(editedItemId.value[0])[0]);

        break;

      default:
        rows.value.push({
          name: name.value,
          age: age.value,
          id: uniqueID(),
        });
        rows.value.reverse();
        break;
    }

    $q.notify({
      icon: "done",
      color: "positive",
      message: `${isEditing.value ? "編輯成功" : "新增成功"}`,
    });

    onReset();
  }
};

const onReset = () => {
  name.value = null;
  age.value = null;

  nameRef.value.resetValidation();
  ageRef.value.resetValidation();

  isEditing.value = false;
  editedItemId.value = null;
};

const handleEdit = (event, { row }) => {
  name.value = row.name;
  age.value = row.age;
  isEditing.value = true;
  editedItemId.value = rows.value;
};

const alert = (event, { row }) => {
  $q.dialog({
    title: "提示",
    message: "是否確定刪除該筆資料?",
    cancel: true,
    persistent: true,
  }).onOk(() => {
    console.log(row, rows.value.splice(row));
    // 刪除該筆row
  });
};

const uniqueID = () => {
  return Math.floor(Math.random() * Date.now());
};
</script>

<style scoped>
.iconWrapper {
  display: flex;
  gap: 1rem;
  align-items: center;
}
.icon {
  font-size: 1.2rem !important;
  cursor: pointer;
}
</style>
