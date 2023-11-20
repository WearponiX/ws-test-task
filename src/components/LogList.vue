<template>
  <div class="page">
    <div class="filter">
      <button
        class="filter__item"
        :class="{ 'filter__item--active': currentFilter === level }"
        v-for="(level, key) in logLevels"
        @click.prevent="toggleFilter(level)"
      >
        {{ level }}
      </button>
      <input
        class="filter__search"
        type="text"
        placeholder="Search"
        v-model="searchQuery"
      />
    </div>
    <ul class="list" v-if="getFilteredLogs">
      <LogItem
        class="list-item"
        :class="`item-${key}`"
        :ref="`item-${key}`"
        v-for="(item, key) in getFilteredLogs"
        :item="item"
        :key="`${item.Timestamp}-${item.Level}`"
        :searchQuery="searchQuery"
      />
    </ul>
    <p class="list" v-else>Not connected yet</p>
    <p class="stats" v-if="getFilteredLogs?.length !== logs.length">
      Filtered: {{ getFilteredLogs?.length }} / {{ logs.length }}
    </p>
  </div>
</template>

<script setup>
import LogItem from "./LogItem.vue";
import { ref, computed, reactive } from "vue";
const api = "ws://test.enter-systems.ru/";

// SEARCH
const searchQuery = ref("");

// SOCKET
let token = "";
let logs = ref([]);

const getFilteredLogs = computed(() => {
  const searchedLogs = ref(
    searchQuery.value === ""
      ? logs.value || []
      : logs.value.filter(
          (el) => String(el.Message).indexOf(String(searchQuery.value)) >= 0
        )
  );
  console.log(searchedLogs || "none");
  return currentFilter.value === ""
    ? searchedLogs.value
    : searchedLogs.value.filter((el) => el?.Level === currentFilter.value);
});

const webSocket = new WebSocket(api);

const init = () => {
  let loginStatus = 1;
  const authentificateUser = () => {
    console.log("Auth");
    const auth = [
      2,
      "1234567890123456",
      "http://enter.local/login",
      {
        username: "enter",
        password: "A505a",
      },
    ];
    webSocket.send(JSON.stringify(auth));
  };

  const authentificateUser2 = () => {
    console.log("Auth2");
    const auth2 = [
      2,
      "1234567890123457",
      "http://enter.local/loginByToken",
      {
        token,
      },
    ];
    webSocket.send(JSON.stringify(auth2));
  };

  const subscribeToLogs = () => {
    console.log("Subbed");
    const sub = [5, "http://enter.local/subscription/logs/list"];
    webSocket.send(JSON.stringify(sub));
  };
  webSocket.onopen = (e) => {
    console.log("Opened");
    authentificateUser();
  };
  webSocket.onmessage = (e) => {
    const res = JSON.parse(e.data);
    if (loginStatus === 3) {
      console.log(getFilteredLogs.value);
      const logLine = res[2]?.Items?.[0];
      if (logLine) logs.value.unshift(logLine);
      return;
    }
    if (loginStatus === 2) {
      subscribeToLogs();
      return (loginStatus = 3);
    }
    if (loginStatus === 1) {
      authentificateUser2();
      return (loginStatus = 2);
    }
  };
};
init();

// FILTER
const currentFilter = ref("");

const logLevels = ["Fatal", "Error", "Debug", "Info", "Trace"];

const toggleFilter = (val) => {
  currentFilter.value === val
    ? (currentFilter.value = "")
    : (currentFilter.value = val);
};
</script>

<style scoped lang="scss">
@mixin button {
  padding: 5px 10px;
  border: 1px solid white;
  border-radius: 10px;
  cursor: pointer;
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  color: #ffffffde;

  border-radius: 10px;
  border: 1px solid transparent;
  padding: 5px 10px;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
  &:hover {
    border-color: #646cff;
  }
  &:focus,
  &:focus-visible {
    border-color: #646cff;
    outline: none;
  }
  &--active {
    border-color: #ffffffde;
    outline: none;
    background: #242424;
    &:hover,
    &:focus,
    &:focus-visible {
      border-color: #ffffffde;
      outline: none;
      background: #242424;
    }
  }
}

.page {
  display: flex;
  flex-flow: column;
  flex-grow: 1;
  height: 100%;
}
.filter {
  display: flex;
  flex-flow: row wrap;
  padding: 0;
  margin: 0;

  &__item {
    margin-right: 5px;

    @include button;
  }

  &__search {
    @include button;
    margin-left: auto;
    border-radius: 10px;
  }
}
.list {
  display: flex;
  flex-flow: column;
  flex-grow: 1;
  height: 100%;
  margin: 10px 0;
  padding: 0;
  overflow: auto;
  gap: 10px;
}
.stats {
  margin: 0;
}
</style>
