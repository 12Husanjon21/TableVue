<template>
  <div class="p-6 bg-gray-100 min-h-screen">
    <h1 class="text-2xl font-bold mb-4 text-center">Dynamic Table</h1>
    <TableComponent :data="tableData" :columns="columns" />
  </div>
</template>

<script>
import TableComponent from "./components/TableComponent.vue";

export default {
  components: {
    TableComponent,
  },
  setup() {
    const tableData = [
      {
        name: "John Brown",
        age: 32,
        tags: "Nice, Developer",
        address: "New York No. 1 Lake Park",
      },
      {
        name: "Jim Green",
        age: 42,
        tags: "Nice, Developer",
        address: "London No. 1 Lake Park",
      },
      {
        name: "Joe Black",
        age: 32,
        tags: "Loser",
        address: "Sidney No. 1 Lake Park",
      },
    ];

    const columns = [
      { key: "name", label: " 🔽 Name " },
      { key: "age", label: "Age" },
      { key: "address", label: "Address" },
      { key: "tags", label: "Tags" },
    ];

    return {
      tableData,
      columns,
    };
  },
};
</script>
