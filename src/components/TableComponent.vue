<template>
  <div class="">
    <table class="min-w-full border border-gray-200 shadow-md rounded-lg">
      <!-- Header -->
      <thead>
        <tr class="bg-gray-200 text-black text-sm font-semibold">
          <th v-for="col in columns" :key="col.key" class="px-6 py-4 text-left">
            {{ col.label }}
          </th>
          <th class="px-6 py-3 text-left">Actions</th>
        </tr>
      </thead>
      <!-- Body -->
      <tbody>
        <tr
          v-for="(row, rowIndex) in editingData"
          :key="rowIndex"
          class="hover:bg-gray-200 border-b"
        >
          <td
            v-for="col in columns"
            :key="col.key"
            class="px-6 py-4 text-sm text-gray-700"
          >
            <span v-if="!isEditing(rowIndex, col.key)">
              {{ row[col.key] }}
            </span>
            <input
              v-else
              v-model="editingData[rowIndex][col.key]"
              class="w-full px-2 py-1 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
          </td>
          <!-- Actions Column -->
          <td class="px-6 py-4 flex gap-2 items-center">
            <button
              v-if="!isEditing(rowIndex)"
              @click="startEdit(rowIndex)"
              class="px-3 py-1 text-blue-600 rounded"
            >
              Edit
            </button>
            <button
              v-if="!isEditing(rowIndex)"
              @click="deleteRow(rowIndex)"
              class="px-3 py-1 text-blue-600 rounded"
            >
              Delete
            </button>
            <div v-if="!isEditing(rowIndex)" class="relative group">
              <button class="px-3 py-1 text-blue-600 rounded">
                More actions
              </button>
              <div
                class="absolute z-10 left-0 mt-1 w-32 bg-white border border-gray-200 shadow-md rounded-lg hidden group-hover:block"
              >
                <ul class="z-10 -mt-1 overflow-hidden">
                  <li class="px-4 cursor-pointer py-2 hover:bg-gray-100">
                    Action 1
                  </li>
                  <li class="px-4 cursor-pointer py-2 hover:bg-gray-100">
                    Action 2
                  </li>
                  <li class="px-4 cursor-pointer py-2 hover:bg-gray-100">
                    Action 3
                  </li>
                </ul>
              </div>
            </div>
            <button
              v-if="isEditing(rowIndex)"
              @click="saveEdit(rowIndex)"
              class="px-3 py-1 text-blue-600 rounded"
            >
              Save
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="undoDelete" class="px-3 py-1 mt-4 text-blue-600 rounded">
      Undo Delete
    </button>
  </div>
</template>

<script>
import { reactive, ref, onMounted, watch } from "vue";

export default {
  props: {
    data: Array,
    columns: Array,
  },
  setup(props) {
    const editingData = reactive(props.data.map((row) => ({ ...row })));
    const editingRow = ref(null);
    const activeDropdown = ref(null);
    const history = ref([]);

    const isEditing = (rowIndex) => {
      return editingRow.value === rowIndex;
    };

    const startEdit = (rowIndex) => {
      editingRow.value = rowIndex;
    };

    const saveEdit = (rowIndex) => {
      for (const key in editingData[rowIndex]) {
        props.data[rowIndex][key] = editingData[rowIndex][key];
      }
      editingRow.value = null;
      saveToLocalStorage();
    };

    const deleteRow = (rowIndex) => {
      history.value.push({
        row: { ...editingData[rowIndex] },
        index: rowIndex,
      });
      props.data.splice(rowIndex, 1);
      editingData.splice(rowIndex, 1);
      saveToLocalStorage();
    };

    const undoDelete = () => {
      const lastDeleted = history.value.pop();
      if (lastDeleted) {
        props.data.splice(lastDeleted.index, 0, lastDeleted.row);
        editingData.splice(lastDeleted.index, 0, lastDeleted.row);
        saveToLocalStorage();
      }
    };

    const saveToLocalStorage = () => {
      localStorage.setItem("tableData", JSON.stringify(editingData));
    };

    const loadFromLocalStorage = () => {
      const storedData = localStorage.getItem("tableData");
      if (storedData) {
        const parsedData = JSON.parse(storedData);
        props.data.splice(0, props.data.length, ...parsedData);
        editingData.splice(0, editingData.length, ...parsedData);
      }
    };

    const toggleDropdown = (rowIndex) => {
      activeDropdown.value =
        activeDropdown.value === rowIndex ? null : rowIndex;
    };

    watch(
      () => props.data,
      (newData) => {
        localStorage.setItem("tableData", JSON.stringify(editingData));
      },
      { deep: true }
    );

    onMounted(() => {
      loadFromLocalStorage();
    });

    return {
      editingData,
      editingRow,
      activeDropdown,
      isEditing,
      startEdit,
      saveEdit,
      deleteRow,
      undoDelete,
    };
  },
};
</script>

<style>
/* Minimal custom styling for the dropdown */
.group:hover .group-hover\:block {
  display: block;
}

.overflow-hidden {
  overflow: hidden;
}
</style>
