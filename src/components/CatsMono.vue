<template>
  <div class="wrapper">
    <Progress v-if="download"></Progress>
    <div class="portrait" v-if="!download">
      <h1 class="head">
        Cтранички котиков
      </h1>
      <h2 class="portrait__head">На подиум выходит - {{cat.id}}!</h2>
      <img class="portrait__photo" :src="cat.url" :width="width" :height="height">
      <ul class="paginator">
        <li class="paginator__last">
          <button class="paginator__btn" @click="onLastCatClick" v-if="this.currentCat > 0">
            На котика назад
          </button>
        </li>
        <li class="paginator__next">
          <button class="paginator__btn" @click="onNextCatClick">
            На котика вперёд
          </button>
        </li>
      </ul>
    </div>
    <Error :msg="error" v-if="error" />
    <button class="return">
      <router-link to="/">В меню</router-link>
    </button>
  </div>
</template>

<script>
import { mapState, mapMutations } from 'vuex';
import Error from '@/components/Error';
import Progress from '@/components/Progress';

const URL_MONO = 'https://api.thecatapi.com/v1/images/search';
const PHOTO_RATIO = 0.6;
const NAMES = [
  'Барсик', 'Боня', 'Бакс', 'Алекс', 'Бади', 'Амур', 'Ебони', 'Абуссель', 'Баксик', 'Жопкинс', 'Кузя',
  'Персик', 'Абрек', 'Абрикос', 'Тимоша', 'Авалон', 'Бабник', 'Саймон', 'Бурбузяка Жабс', 'Абу', 'Марсик', 'Аадон',
  'Маркиз', 'Дымок', 'Лаки', 'Симба', 'Абрамович', 'Сёма', 'Пушок', 'Айс', 'Бося', 'Алмаз', 'Кекс', 'Басик',
  'Макс', 'Феликс', 'Гарфилд', 'Том', 'Тиша', 'Цезарь', 'Тишка', 'Мася', 'Абакан', 'Лакки', 'Васька', 'Адольф',
  'Марсель', 'Бабасик', 'Вася', 'Зевс', 'Вольт', 'Адидас', 'Лео', 'Зефир', 'Максик', 'Вайс', 'Барс', 'Кокос',
  'Рыжик', 'Айс-Крим', 'Мартин', 'Филя', 'Томас', 'Нафаня', 'Абориген', 'Валера', 'Дарсик', 'Марс', 'Базиль',
  'Тошка', 'Сосисыч', 'Абрико', 'Абус', 'Абсент', 'Масик', 'Жужа', 'Веня', 'Какаш', 'Умка', 'Каспер', 'Грей',
  'Убийца мышей', 'Живчик', 'Глюк', 'Виски', 'Патрик', 'Оптимус Прайм', 'Баффи', 'Акакий', 'Яндекс', 'Симка',
  'Тёма', 'Гаврик', 'Аватар', 'Жан батист Гренуй', 'Ганс', 'Вегас', 'Базилио', 'Кокс', 'Байрон', 'Бонни',
  'Гаврюша', 'Люцифер', 'Вин Дизель', 'Вафлик', 'Авдон', 'Снежок', 'Тима', 'Август', 'Ба-зу', 'Яша', 'Абраша',
  'Абсолют', 'Абис', 'Секас', 'Абель', 'Базик', 'Даня', 'Абар', 'Оскар', 'Абдул', 'Абс', 'Байт', 'Сэм', 'Авгит',
  'Абрам', 'Абумайло', 'Аванти', 'Абилен', 'Бальтазар', 'Базилевс', 'Жора', 'Сеня', 'Баламут', 'Абрантес', 'Абен',
  'Абеляр', 'Ганжубас', 'Джокер', 'Пушистик', 'Риччи', 'Багет', 'Ричард Львиное Сердце', 'Байи', 'Базилиано',
  'Джек Воробей', 'Бабай', 'Авгур', 'Августин', 'Вавилон', 'Байсик', 'Валет', 'Базил', 'Байк', 'Байрам', 'Валдис',
  'Абур', 'Еврик', 'Изя', 'Абрахам', 'Ван Гог', 'АбиАбо', 'Вазилин', 'Багратион', 'Тимофей', 'Мурзик', 'Аберден',
  'Гоша', 'Гай', 'Валик', 'Бабстер', 'Байдар', 'Валерьян', 'Балу', 'Валли', 'Базилион', 'Валленштейн', 'Гандурас',
  'Гав', 'Вальс', 'Гангстер', 'Баггер', 'Базальт', 'Байкал', 'Шашлык', 'Базилька', 'Коксик', 'Августус', 'Ежик',
  'Найк', 'Базель', 'Ральф', 'Або-Анс', 'Бакстер', 'Вальмонт', 'Женя', 'Мажор', 'Абль', 'Бай', 'Вискас', 'Даниэль',
  'Базан', 'Егорка', 'Азазелло', 'Байард', 'Малыш', 'Байбурт', 'Барон', 'Або', 'Зая', 'Гарри', 'Кексик', 'Кеша',
  'Котя', 'Тайсон', 'Дарт Вейдер', 'Яшка', 'Сапфир', 'Едди', 'Вальдо', 'Греси Ройс Хорес', 'Дак', 'Вальдемар', 'Майк',
  'Бак', 'Рич', 'Найс', 'Йосик', 'Гамлет', 'Багор', 'Тимон', 'Беня', 'Бабет', 'Лексус', 'Валенок', 'Укроп',
  'Давид', 'Кабаясик', 'Лёва', 'Габриэль', 'Василий', 'Ватсон', 'Егор', 'Демон', 'Джастин', 'Маврик', 'Гарольд',
  'Баксентий', 'Ерошка', 'Фунтик', 'Йогурт', 'Доминик', 'Крис', 'Микки', 'Маленький Дьявол', 'Сава', 'Ярик',
  'Елисей', 'Лекс', 'Оливер', 'Изюмчик', 'Паштет', 'Байярд', 'Чувак', 'Йорик', 'Миша', 'Ягуар', 'Тимошка',
  'Йода', 'Тормоз', 'Митяй', 'Данателло', 'Оззи', 'Даник', 'Сникерс', 'Зак', 'Ларс фон Триер', 'Бенджамен',
  'Жиган', 'Граф', 'Адам', 'Шустрик', 'Дариус', 'Лаваш', 'Емеля', 'Дар', 'Валькер', 'Валентин', 'Бенджамин',
  'Лапа', 'Кайзер', 'Вакх', 'Гамми', 'Изи', 'Дымка', 'Гуччи', 'Захар', 'Гарсон', 'Еврасик', 'Валек',
];
export default {
  name: 'CatsMono',
  components: { Progress, Error },
  data() {
    return {
      cat: null,
      width: 700,
      height: 420,
      download: false,
      error: null,
    };
  },
  created() {
    this.getCat();
    window.addEventListener('resize', this.onResizeWindow);
  },
  destroyed() {
    window.removeEventListener('resize', this.onResizeWindow);
  },
  computed: mapState(['currentCat', 'HEADERS']),
  methods: {
    getCat() {
      this.download = true;
      if (!sessionStorage.getItem(`photo${this.currentCat}`)) {
        fetch(URL_MONO, this.HEADERS).then(
          (response) => {
            if (response.ok) {
              response.json().then(
                (json) => {
                  this.cat = {
                    url: json[0].url,
                    id: NAMES[Math.floor(Math.random() * NAMES.length)],
                  };
                  sessionStorage.setItem(`photo${this.currentCat}`, JSON.stringify(this.cat));
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
        this.cat = JSON.parse(sessionStorage.getItem(`photo${this.currentCat}`));
        this.download = false;
      }
    },
    onLastCatClick() {
      this.decrementCat();
      this.getCat();
    },
    onNextCatClick() {
      this.incrementCat();
      this.getCat();
    },
    onResizeWindow() {
      if (document.documentElement.clientWidth <= 700) {
        this.width = document.documentElement.clientWidth;
        this.height = document.documentElement.clientWidth * PHOTO_RATIO;
      } else {
        this.width = 700;
        this.height = 420;
      }
    },
    ...mapMutations(['incrementCat', 'decrementCat']),
  },
};
</script>

<style lang="less">
  .wrapper {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: space-between;
    height: 100vh;
    background-image: linear-gradient(
        rgba(255, 255, 255, 0.8),
        rgba(0, 0, 0, 0.5))
  }

  .head {
    width: 100%;
    height: 40px;
    padding-top: 10px;
    text-align: center;
    font-size: 24px;
    line-height: 28px;
  }

  .portrait {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .portrait__head {
    width: 100%;
    text-align: center;
  }

  .portrait__photo {
    border-radius: 20px;
    margin-bottom: 20px;
  }

  .paginator {
    width: 100%;
    margin: 0 auto 10px;
    padding: 0 10px;
    box-sizing: border-box;
    display: flex;
    justify-content: center;
  }

  .paginator__last {
    margin-right: 30px;
  }

  .return {
    margin-bottom: 20px;
  }
  @media (max-width: 320px) {
    .head {
      width: 320px;
      font-size: 18px;
    }

    .portrait__head {
      width: 320px;
      font-size: 14px;
    }

    .paginator {
      width: 320px;
      flex-wrap: wrap;
    }

    .paginator__btn {
      width: 140px;
    }

    .paginator__last {
      margin-right: 5px;
      margin-bottom: 5px;
    }
  }
  @media (max-width: 880px) {
    .wrapper {
      height: max(100%, 644);
    }
  }
</style>
