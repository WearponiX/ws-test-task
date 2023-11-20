<template>
  <li class="log-item" v-if="item">
    <p class="timestamp">[{{ item?.Timestamp }}]</p>
    <p class="level">Level: {{ item?.Level }}</p>
    <p class="source" v-show="item?.Source">Source: {{ item.Source }}</p>
    <p class="message" v-html="`Message: ${message}`" />
  </li>
</template>

<script setup>
import { ref, reactive, computed } from "vue";
const { item, searchQuery } = defineProps({
  item: {
    required: true,
    type: Object,
  },
  searchQuery: {
    required: true,
    type: String,
  },
});

const query = reactive(searchQuery);
const message = computed(() => {
  return item?.Message.replaceAll(query.value, `<span>${query.value}</span>`);
});
</script>

<style lang="scss">
.log-item {
  display: flex;
  flex-flow: row wrap;
  text-align: left;
  .timestamp,
  .source,
  .level {
    display: flex;
    width: 30%;
    margin: 0;
    color: #fff;
    font-size: 12px;
    &:not(:first-child) {
      margin-left: 5px;
    }
  }

  .message {
    margin: 0;
    font-size: 14px;
    color: #aaa;

    & span {
      color: #9acd32;
    }
  }
}
</style>
