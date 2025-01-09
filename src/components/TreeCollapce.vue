<template>
  <ul class="tree-collapse">
    <li v-for="(node, index) in treeData" :key="index">
      <div
        class="tree-node"
        :class="{ 'has-children': node.children }"
        @click="toggleNode(index)"
      >
        <span v-if="node.children" class="toggle-icon">
          {{ isOpen[index] ? "▼" : "▶" }}
        </span>
        <span>{{ node.label }}</span>
      </div>
      <TreeCollapse
        v-if="node.children && isOpen[index]"
        :treeData="node.children"
        class="child-tree"
      />
    </li>
  </ul>
</template>

<script>
import { ref } from "vue";

export default {
  name: "TreeCollapse",
  props: {
    treeData: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    const isOpen = ref(props.treeData.map(() => false));

    const toggleNode = (index) => {
      isOpen.value[index] = !isOpen.value[index];
    };

    return {
      isOpen,
      toggleNode,
    };
  },
};
</script>

<style scoped>
.tree-collapse {
  list-style: none;
  padding-left: 20px;
  font-family: Arial, sans-serif;
}

.tree-node {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 4px 0;
  user-select: none;
}

.tree-node.has-children .toggle-icon {
  margin-right: 8px;
  font-size: 12px;
}

.tree-node:hover {
  background-color: #f0f0f0;
}

.child-tree {
  margin-left: 15px;
}
</style>
