<script setup lang="ts">
import { useStudentStore } from "~/stores/studentStore";
import { ref } from "vue";

const props = defineProps<{
  label: string;
}>();

const studentStore = useStudentStore();

const isOpen = ref(false);
const formData = ref({
  lastName: "",
  firstName: "",
  middleName: "",
  birthDate: "",
  classNumber: '',
  classLetter: '',
  gender: "",
});
const classes = ["5", "6", "7", "8", "9"];
const letters = ["А", "Б", "В", "Г", "Д"];
const selectGender = (gender: string) => {
  formData.value.gender = gender;
};
// Функция для отправки данных на сервер через хранилище
const createStudent = async () => {
  try {
    const studentData = {
      ...formData.value,
    };

    // Добавляем студента через хранилище
    await studentStore.addStudent(studentData);

    // Закрываем модальное окно после успешного создания
    isOpen.value = false;

    // Очищаем форму
    formData.value = {
      lastName: "",
      firstName: "",
      middleName: "",
      birthDate: "",
      classNumber: '',
      classLetter: '',
      gender: "",
    };

    // Можно добавить уведомление об успешном создании
    console.log("Студент создан");
  } catch (error) {
    console.error("Ошибка при создании студента:", error);
  }
};
</script>

<template>
  <ClientOnly>
    <div>
      <UButton :label="label" @click="isOpen = true" />

      <UModal v-model="isOpen">
        <div class="p-4 m-4 flex gap-2 flex-col">
          <UInput v-model="formData.lastName" placeholder="Фамилия" />
          <UInput v-model="formData.firstName" placeholder="Имя" />
          <UInput v-model="formData.middleName" placeholder="Отчество" />
          <UInput
            v-model="formData.birthDate"
            placeholder="Дата рождения"
            type="date"
          />
          <div class="flex gap-2">
            <USelect v-model="formData.classNumber" :options="classes" />
            <USelect v-model="formData.classLetter" :options="letters" />
          </div>
          <div class="flex gap-2">
            <UCheckbox
              label="Муж"
              :model-value="formData.gender === 'male'"
              @update:model-value="selectGender('male')"
            />
            <UCheckbox
              label="Жен"
              :model-value="formData.gender === 'female'"
              @update:model-value="selectGender('female')"
            />
          </div>

          <UButton
            icon="i-heroicons-pencil-square"
            size="2xs"
            color="primary"
            variant="solid"
            label="Создать студента"
            :trailing="false"
            @click="createStudent"
          />
        </div>
      </UModal>
    </div>
  </ClientOnly>
</template>
