<template>
  <main id="app">
    <router-view/>
  </main>
</template>

<script>
import ManagerCats from '@/components/ManagerCats';
import CatsMono from '@/components/CatsMono';
import CatList from '@/components/CatList';
import Vue from 'vue';
import VueRouter from 'vue-router';
import Vuex from 'vuex';

Vue.use(VueRouter);
Vue.use(Vuex);

const routes = [
  {
    path: '/',
    name: 'menu',
    component: ManagerCats,
  },
  {
    path: '/cat-mono',
    name: 'mono',
    component: CatsMono,
    props: true,
  },
  {
    path: '/cat-list',
    name: 'list',
    component: CatList,
    props: true,
  },
];

const router = new VueRouter({
  routes,
});

const store = new Vuex.Store({
  state: {
    currentCat: 0,
    currentPage: 0,
    HEADERS: {
      'x-api-key': '80aaeb1b-2a47-4fa9-a8aa-d52215f905f2',
    },
  },
  mutations: {
    incrementCat(state) {
      state.currentCat += 1;
    },
    decrementCat(state) {
      state.currentCat -= 1;
    },
    incrementPage(state) {
      state.currentPage += 1;
    },
    decrementPage(state) {
      state.currentPage -= 1;
    },
  },
});

export default {
  name: 'App',
  router,
  store,
  components: {
  },
};
</script>

<style lang="less">
  body {
    margin: 0;
  }
  main {
    font-family: "Segoe Print", "Times New Roman", sans-serif;
    font-size: 16px;
  }

  h1, h2, ul, li {
    margin: 0;
    padding: 0;
  }
  a {
    color: inherit;
    text-decoration: none;
  }
  ul {
    list-style: none;
  }
  button {
    font-family: inherit;
    font-size: 24px;
    padding: 0 15px 0 15px;

    border: none;
    border-radius: 20px;
    background-color: bisque;
    box-shadow: 2px 2px 2px 0 #7c6d6d;

    &:focus {
       outline: none;
     }
  }

  :-moz-submit-invalid {
    box-shadow: none;
  }

  :-moz-ui-invalid {
    box-shadow:none;
  }

  @media (max-width: 320px) {
    main {
      font-size: 14px;
    }

    button {
      font-size: inherit;
    }
  }
</style>
