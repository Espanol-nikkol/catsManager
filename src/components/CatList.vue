<template>
  <div class="wrapper">
    <Progress v-if="download"></Progress>
    <div class="albums" v-if="!download">
      <h2 class="head">
        Больше котиков любителям котиков!
      </h2>
      <ul class="cat-list">
        <li class="cat-list__el" v-for="cat in cats" :key="cat.index">
          <img class="albums__photo" :src="cat" :width="width" :height="height">
        </li>
      </ul>
      <ul class="paginator">
        <li class="paginator__last">
          <button @click="onLastCatsClick" v-if="this.currentPage > 0">
            На много котиков назад
          </button>
        </li>
        <li class="paginator__next">
          <button @click="onNextCatsClick">
            На много котиков вперёд
          </button>
        </li>
      </ul>
    </div>
    <Error :msg="error" v-if="error"></Error>
    <button class="return">
      <router-link to="/">В меню</router-link>
    </button>
  </div>

</template>

<script>
import { mapState, mapMutations } from 'vuex';
import Error from '@/components/Error';
import Progress from '@/components/Progress';

const URL_LIST = 'https://api.thecatapi.com/v1/images/search?limit=12';

export default {
  name: 'CatList',
  components: { Progress, Error },
  data() {
    return {
      download: false,
      cats: null,
      width: 220,
      height: 180,
      error: null,
    };
  },
  created() {
    this.getCatList();
  },
  computed:
      mapState(['HEADERS', 'currentPage']),
  methods: {
    getCatList() {
      this.download = true;
      if (!sessionStorage.getItem(`page${this.currentPage}`)) {
        fetch(URL_LIST, this.HEADERS).then(
          (response) => {
            if (response.ok) {
              response.json().then(
                (json) => {
                  this.cats = json.map((i) => i.url);
                  sessionStorage.setItem(`page${this.currentPage}`, JSON.stringify(this.cats));
                  this.download = false;
                },
              );
            } else {
              this.error = `Ошибка HTTP ${response.status}.Пожалуйста, попробуйте позже ещё раз или обратитесь к разработчику`;
            }
          },
        ).catch(() => {
          this.error = 'Ошибка соединения, проверьте наличие связи';
        });
      } else {
        this.cats = JSON.parse(sessionStorage.getItem(`page${this.currentPage}`));
        this.download = false;
      }
    },
    onLastCatsClick() {
      this.decrementPage();
      this.getCatList();
    },
    onNextCatsClick() {
      this.incrementPage();
      this.getCatList();
    },
    ...mapMutations(['incrementPage', 'decrementPage']),
  },
};
</script>

<style lang="less">
  .albums {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .albums__head {
    width: 100%;
  }

  .cat-list {
    width: 880px;
    display: flex;
    flex-wrap: wrap;
    margin-bottom: 10px;
  }

  .cat-list__el {
    height: 180px;
  }

  @media (max-width: 880px) {
    .cat-list {
      width: 100%;
      margin-top: 20px;
      justify-content: center;
    }
  }

</style>
