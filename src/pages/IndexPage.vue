<template>
  <div class="q-pa-md" style="max-width: 300px">
    <form
      @submit.prevent.stop="onSubmit"
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
        <q-btn label="新增" type="submit" color="primary" />
        <q-btn label="清除" type="reset" color="primary" flat class="q-ml-sm" />
      </div>
    </form>
    <q-table title="" :rows="rows" :columns="columns" row-key="name">
      <template v-slot:body-cell-icon="props">
        <q-td :props="props" class="iconWrapper">
          <q-icon
            class="icon editIcon"
            name="fa-regular fa-pen-to-square"
            size="lg"
            @click="handleEdit(rows)"
          />
          <q-icon class="icon deleteIcon" name="fa-solid fa-trash"></q-icon>
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
      message: "請確認您輸入資料",
    });
  } else {
    rows.value.push({
      name: name.value,
      age: age.value,
    });
    rows.value.reverse();

    $q.notify({
      icon: "done",
      color: "positive",
      message: "新增成功",
    });

    onReset();
  }
};

const onReset = () => {
  name.value = null;
  age.value = null;

  nameRef.value.resetValidation();
  ageRef.value.resetValidation();
};

const handleEdit = (rows) => {
  console.log(rows);
};
</script>

<style scoped>
.iconWrapper {
  display: flex;
  gap: 1rem;
}
.icon {
  font-size: 1.2rem !important;
}
</style>
