<template>
  <div class="py-6 px-4">
    <input
      type="text"
      class="w-64 border px-2 py-1 text-gray-900 focus:outline-none"
      placeholder="Search"
      :value="search"
      @input="handleSearch"
    />
    <div class="mt-2">
      <p v-if="loading">Loading...</p>
      <ul v-else>
        <li
          class="list-inside list-disc py-1"
          v-for="todo in todos"
          :key="todo.is"
        >
          {{ todo.title }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import _ from 'lodash';

export default {
  name: 'App',

  data() {
    return {
      loading: true,
      search: '',
      todos: [],
      timeout: null,
    };
  },

  created() {
    axios
      .get('https://jsonplaceholder.typicode.com/todos')
      .then((response) => {
        setTimeout(() => {
          this.todos = response.data.slice(0, 10);
          this.loading = false;
        }, 1000);
      })
      .catch((error) => console.log(error.response));
  },

  methods: {
    debounceFn(func, wait) {
      let timeout;

      return (...args) => {
        if (timeout) clearTimeout(timeout);

        timeout = setTimeout(() => func(...args), wait);
      };
    },

    // handleSearch(e) {
    //   this.search = null;
    //   if (this.timeout) clearTimeout(this.timeout);
    //   this.timeout = setTimeout(() => {
    //     console.log(e.target.value);
    //   }, 1000);
    // },

    handleSearch(e) {
      this.loading = true;
      this.filterSearch(e);
    },

    filterSearch: _.debounce(function (e) {
      this.search = e.target.value;

      axios
        .get('https://jsonplaceholder.typicode.com/todos')
        .then(({data}) => {
          this.todos = data.filter((item) =>
            item.title.toLowerCase().includes(this.search.toLowerCase())
          );
          this.loading = false;
        })
        .catch((error) => console.log(error.response));
    }, 1000),
  },
};
</script>
