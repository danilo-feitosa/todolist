<script setup lang="ts">
interface Task {
  id: number
  text: string
  completed: boolean
}

const value = ref("");
const tasks = ref<Task[]>([]);
const isOpen = ref(false);
const editingTask = ref<number | null>(null);

const addTask = () => {
  if (!value.value.trim()) return;
  
  tasks.value.push({
    id: Date.now(),
    text: value.value,
    completed: false
  });
  value.value = "";
};

const deleteTask = (index: number) => {
  tasks.value.splice(index, 1);
};

const editTask = (index: number) => {
  editingTask.value = index;
  value.value = tasks.value[index].text;
  isOpen.value = true;
};

const saveEditedTask = () => {
  if (editingTask.value !== null && value.value.trim()) {
    tasks.value[editingTask.value].text = value.value;
    value.value = "";
    editingTask.value = null;
    isOpen.value = false;
  }
};

const cancelEdit = () => {
  value.value = "";
  editingTask.value = null;
  isOpen.value = false;
};

const toggleTask = (index: number) => {
  tasks.value[index].completed = !tasks.value[index].completed;
};

const handleKeyPress = (event: KeyboardEvent) => {
  if (event.key === 'Enter') {
    if (isOpen.value) {
      saveEditedTask();
    } else {
      addTask();
    }
  }
};
</script>

<template>
  <div class="flex items-center h-screen flex-col font-['Ubuntu']">
    <h1 class="text-6xl font-bold mt-20">To-Do List</h1>
    <p class="text-xl font-medium">with Nuxt by Danilo Feitosa</p>
    <div class="flex gap-2 my-4">
      <UInput
        color="teal"
        variant="outline"
        placeholder="Digite uma tarefa..."
        v-model="value"
        @keyup.enter="handleKeyPress"
      />
      <UButton 
        label="Adicionar" 
        @click="addTask" 
        :disabled="!value.trim()"
        color="teal"
      />
    </div>
    <ul class="w-full max-w-2xl space-y-3">
      <li
        class="list-none flex items-center justify-between gap-3 bg-gray-50 border px-4 py-2 rounded-md"
        v-for="(task, index) in tasks"
        :key="task.id"
      >
        <UToggle
          on-icon="i-heroicons:check-20-solid"
          off-icon="i-heroicons:x-mark-20-solid"
          :model-value="task.completed"
          @update:model-value="toggleTask(index)"
        />
        <p class="text-2xl font-semibold mr-20" :class="{ 'line-through text-green-500': task.completed }">
          {{ task.text }}
        </p>
        <div class="flex items-center gap-3">
          <UButton
            icon="i-heroicons:pencil-square-20-solid"
            color="gray"
            variant="solid"
            :trailing="false"
            @click="editTask(index)"
          />
          <UButton
            icon="i-heroicons:trash-20-solid"
            color="red"
            variant="solid"
            :trailing="false"
            @click="deleteTask(index)"
          />
        </div>
      </li>
    </ul>
    <UModal v-model="isOpen">
      <div class="p-4 space-y-4">
        <h3 class="text-xl font-semibold">Editar Tarefa</h3>
        <UInput
          v-model="value"
          placeholder="Editar tarefa..."
          @keyup.enter="saveEditedTask"
          color="teal"
        />
        <div class="flex justify-end gap-2">
          <UButton
            label="Cancelar"
            variant="soft"
            color="gray"
            @click="cancelEdit"
          />
          <UButton
            label="Salvar"
            color="teal"
            :disabled="!value.trim()"
            @click="saveEditedTask"
          />
        </div>
      </div>
    </UModal>
  </div>
</template>
