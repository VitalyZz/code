<template>
  <div v-if="showLoader" class="wrapperLoader">
    <Loader/>
  </div>

  <div class="main" v-else>
    <div class="leftSide" v-if="isActiveLeftSide">
      <div class="block-change-statistics">
        <div class="img-document">
          <img :src="img_document" alt="document">
          <span>Текст</span>
        </div>
        <button v-if="accessToUpdate" class="change" @click="showModalEditor = true">Изменить</button>
        <button class="statistics" @click="showModalStatistics = true">Статистика</button>
      </div>

      <div class="highlight" v-if="isDataLoaded">
        <RadioButton :isHighlight="radioBtnText" @click="radioBtnHandle">Выделять текст</RadioButton>
        <RadioButton :isHighlight="radioBtnBackground" @click="radioBtnHandle">Выделять задний фон текста</RadioButton>
      </div>

      <div class="colors" v-if="isDataLoaded">
        <ColorPicker
          v-for="picker in colorPickers"
          :key="picker.index"
          :name="picker.name"
          :color="picker.currentColor"
        >
          {{ picker.text }}
        </ColorPicker>
        <button class="default" @click="setDefaultColors">Установить по умолчанию</button>
      </div>

      <div class="changeTheme">
        <div class="wrapper_toggle" @click="changeBackground">
          <div class="toggle" :style="{ borderColor: isDark ? '#0DFF92' : 'rgb(162, 172, 191)', color: isDark ? '#0DFF92' : 'rgb(162, 172, 191)' }">
            <span class="toggle_title" :style="{ transform: isDark ? '' : 'translateX(20px)' }">{{ isDark ? 'ON' : 'OFF' }}</span>
            <div class="toggle_circle" :style="{ transform: isDark ? '' : 'translateX(-31px)', background: isDark ? '#0DFF92' : 'rgb(162, 172, 191)' }"></div>
          </div>
          <span class="darkTheme" :style="{ color: isDark ? '#0DFF92' : 'rgb(162, 172, 191)' }">
            {{ isDark ? 'Темная тема' : 'Светлая тема' }}
          </span>
        </div>
      </div>

      <div class="block_hide">
        <button class="hide" @click="isActiveLeftSide = !isActiveLeftSide">Скрыть</button>
      </div>

    </div>

    <div class="openLeftSide" @click="isActiveLeftSide = !isActiveLeftSide" v-else>
      <img :src="arrow" alt="" class="arrow">
    </div>

    <div class="rightSide" :style="{ paddingLeft: isActiveLeftSide ? '10px' : '25px', backgroundColor: isDark ? '#36404A' : '#fff', color: isDark ? '#fff' : '#000' }">
      <p class="text">
        <Word v-for="item in items" :key="item.id_text" :word="item" @wordHandler="wordHandler" :highlight="whatIsHighlight"/>
      </p>
    </div>
  </div>

  <teleport to="body">
    <ModalChangeMeaning
      v-if="isWordClick"
      :x="x"
      :y="y"
      @modalMenuHandler="modalMenuHandler"
    />
  </teleport>

  <teleport to="body">
    <ModalStatistics
        v-if="showModalStatistics"
        @closeModalStatistics="showModalStatistics = false"
        :id_text="$route.params.id"
        :publicAccess="publicAccess"
    />
  </teleport>

  <teleport to="body">
    <ModalEditor
        v-if="showModalEditor"
        @closeModalEditor="closeModalEditor"
        :text="text"
    />
  </teleport>
</template>

<script>
import RadioButton from "@/components/app/RadioButton";
import ColorPicker from "@/components/texts/ColorPicker";
import ModalStatistics from "@/components/texts/ModalStatistics";
import ModalEditor from "@/components/texts/ModalEditor";
import Loader from "@/components/app/Loader";
import Word from "@/components/texts/Word";
import ModalChangeMeaning from "@/components/texts/ModalChangeMeaning";
import axios from "axios";
import {mapGetters, mapMutations} from "vuex";
import img_document from '@/assets/images/document.svg';
import arrow from '@/assets/images/arrow.svg';

export default {
  data() {
    return {
      arrow,
      img_document,
      radioBtnText: false,
      radioBtnBackground: false,
      colorPickers: [
        {name: 'unknown', text: 'Цвет для неизвестных слов', defaultColor: '#ff392e', currentColor: null, index: 0},
        {name: 'known', text: 'Цвет для известных слов', defaultColor: '#5dff34', currentColor: null, index: 1},
        {name: 'study', text: 'Цвет для изучаемых слов', defaultColor: '#5574ff', currentColor: null, index: 2}
      ],
      showModalStatistics: false,
      showModalEditor: false,
      isDataLoaded: false,
      text: '',
      showLoader: true,
      items: [],
      isWordClick: false,
      x: 0,
      y: 0,
      currentWord: {},
      accessToUpdate: false,
      publicAccess: '0',
      isActiveLeftSide: true,
      isDark: true
    }
  },
  computed: {
    ...mapGetters('colors', ['getColorGetter']),
    changeColorsKnown() {
      return this.$store.state['colors'].colors.known;
    },
    changeColorsStudy() {
      return this.$store.state['colors'].colors.study;
    },
    changeColorsUnknown() {
      return this.$store.state['colors'].colors.unknown;
    },
    whatIsHighlight() {
      return this.radioBtnText ? 'color' : 'backgroundColor'
    },
  },
  watch: {
    changeColorsKnown() {
      this.items.filter(el => {
        if (el.class === 'known') el.color = this.getColorGetter('known');
      });
    },
    changeColorsStudy() {
      this.items.filter(el => {
        if (el.class === 'study') el.color = this.getColorGetter('study');
      });
    },
    changeColorsUnknown() {
      this.items.filter(el => {
        if (el.class === 'unknown') el.color = this.getColorGetter('unknown');
      });
    }
  },
  async mounted() {
    const isDark = localStorage.getItem('isDark');

    if (isDark !== null && isDark !== 'true') {
      this.isDark = false
    }

    const id_user = this.$store.getters['auth/getCurrentUser'].id;
    const id_text = this.$route.params.id;

    // Проверка на public для текста
    const publicText = (await axios.post('/text/checkPublic', { id_text, id_user })).data.public;

    console.log()

    let responseText = '';

    if (publicText !== '1') {
      this.accessToUpdate = true
      responseText = (await axios.post('/text/getById', {id_user, id_text})).data
    } else {
      this.publicAccess = '1';
      responseText = (await axios.post('/text/getByIdWithoutUser', {id_user, id_text})).data
    }

    this.text = responseText.text

    this.colorPickers.forEach(el => {
      const currentColor = localStorage.getItem('color_' + el.name);
      this.$store.state['colors'].colors[el.name] = currentColor;
      el.currentColor = currentColor !== null ? currentColor : el.defaultColor;
    })

    const highlight = localStorage.getItem('highlight');

    if (highlight !== null && highlight === 'background') {
      this.radioBtnText = false
      this.radioBtnBackground = true
    } else {
      this.radioBtnText = true
      this.radioBtnBackground = false
    }

    await this.algorithm();

    this.isDataLoaded = true
    this.showLoader = false
  },
  methods: {
    ...mapMutations('colors', ['setColor']),
    changeBackground() {
      this.isDark = !this.isDark;
      localStorage.setItem('isDark', this.isDark.toString())
    },
    async modalMenuHandler(meaning) {
      const info = {
        'known': 0,
        'study': 1,
        'unknown': 2,
      };

      const getClass = {
        0: 'known',
        1: 'study',
        2: 'unknown',
      };

      const id_user = this.$store.getters['auth/getCurrentUser'].id;
      const id_word = this.currentWord.id_word;
      const word = this.currentWord.name;

      this.items.filter(el => {
        if (el.name === word) {
          el.class = getClass[info[meaning]];
          el.color = this.getColor(getClass[info[meaning]]);
        }
      });

      console.log('this.items', this.items)

      this.isWordClick = false;

      if (info[meaning] === 2) {
        await axios.post('/word/deleteWithoutDictionary', {id_user, id_word })
      } else {
        // get yandex START

        let translations = [];

        if (id_word === null) {
          const key = "dict.1.1.20210601T151729Z.cb8f7f225a387d74.67724ff1c9c838097dba9aace6250395f85202d2";

          const url = `https://dictionary.yandex.net/api/v1/dicservice.json/lookup?key=${key}&lang=en-ru&text=${word}`;
          const data = await fetch(url);
          const result = await data.json();

          result.def.forEach(el => {
            el.tr.forEach(el2 => {
              translations.push(el2.text)
            })
          })

          translations = translations.slice(0, 4);

          console.log('translations', translations);

          // for (const translation of translations) {
          //   await axios.post('/words/insertTranslation', {translation, id_word})
          // }
        }

        // get yandex END

        const newIdWord = (await axios.post('/word/changeMeaning', { meaning: info[meaning], id_user, id_word, word, translations })).data

        if (id_word === null) {
          try {
            this.items.find(el => el.name === word).id_word = newIdWord;
          } catch (e) {
            console.log(e);
          }
        }
      }
    },
    wordHandler(word, event) {
      this.isWordClick = true;
      this.currentWord = word;

      const values = event.target.getBoundingClientRect();

      this.x = values.left;
      // this.y = values.top + values.height + 5;

      // this.x = event.pageX;
      this.y = event.pageY + 5;

      console.log('x:', values.left, ' | ', event.pageX);
      console.log('y:', values.top + values.height + 5, ' | ', event.pageY);
      console.log('Event:', event);
    },
    async algorithm() {
      let text = this.text; // здесь считывается весь текст

      const id_user = this.$store.getters['auth/getCurrentUser'].id;
      const resultKnown = (await axios.post('/words/getByMeaning', { id_user, meaning: 0 })).data;
      const resultStudy = (await axios.post('/words/getByMeaning', { id_user, meaning: 1 })).data;

      const arrOfWordsKnown = resultKnown.map(el => el.word);
      const arrOfWordsStudy = resultStudy.map(el => el.word);

      console.log('resultKnown', resultKnown);
      console.log('resultStudy', resultStudy);

      // Переменные для дальнейшей работы в while
      let items = [];
      let textMarch;
      let word;
      let end;
      let nameClass;
      let color;

      let id_word = 0;

      const colors = {
        known: this.getColor('known'),
        study: this.getColor('study'),
        unknown: this.getColor('unknown')
      }

      console.log('colors', colors)

      // Заносим все слова в спаны
      while (true) {
        textMarch = text.match(/[a-z]+/i); // проверяме есть ли совпадение
        if (textMarch === null) { // если конец выходим
          break;
        }
        word = textMarch[0]; // берем найденное слово

        if (arrOfWordsKnown.indexOf(word.toLowerCase()) !== -1) {
          nameClass = 'known';
          color = colors.known;
          id_word = resultKnown.find(el => el.word === word.toLowerCase()).id_word
        } // проверям является ли слово известным

        else if (arrOfWordsStudy.indexOf(word.toLowerCase()) !== -1) {
          nameClass = 'study';
          color = colors.study;
          id_word = resultStudy.find(el => el.word === word.toLowerCase()).id_word
        } // проверям является ли слово изучаемым
        else {
          nameClass = 'unknown';
          color = colors.unknown;
          id_word = null;
        } // иначе слово неизвестное

        end = text.split(word); // получаем 2 массива того что лежит по бокам слова

        items.push({
          before: end[0],
          name: word.toLowerCase(),
          text: word,
          class: nameClass,
          id_word,
          color
        })

        text = text.substr(text.indexOf(word) + word.length);
      }

      this.items = items;
    },
    radioBtnHandle() {
      this.radioBtnText = !this.radioBtnText;
      this.radioBtnBackground = !this.radioBtnBackground;

      localStorage.setItem('highlight', this.radioBtnText ? 'color' : 'background')
    },
    setDefaultColors() {
      this.colorPickers.forEach(el => {
        localStorage.setItem('color_' + el.name, el.defaultColor);

        console.log(el.name, el.defaultColor);

        this.setColor({name: el.name, color: el.defaultColor})
        el.currentColor = el.defaultColor;
        el.index += 3;
      });
    },
    getColor(name) {
      return this.getColorGetter(name) ?? this.colorPickers.find(el => el.name === name).defaultColor;
    },
    async closeModalEditor(updatedText) {
      const id_user = this.$store.getters['auth/getCurrentUser'].id;
      const id_text = this.$route.params.id;

      await axios.post('/text/update', {id_user, id_text, text: updatedText})

      this.text = updatedText
      this.showModalEditor = false

      await this.algorithm();
    }
  },
  components: {
    RadioButton,
    ColorPicker,
    ModalStatistics,
    ModalEditor,
    Loader,
    Word,
    ModalChangeMeaning
  }
}
</script>

<style scoped lang="scss">
$colorBtnChange: #ffd700;
$colorBtnStatistics: #2fb0ff;
$colorBtnDefault: #bf4bff;
$colorBtnHide: #ff621c;

  .wrapperLoader {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .main {
    width: 100%;
    color: #fff;
    display: flex;
    flex-direction: row;
    line-height: 35px;

    .leftSide {
      min-height: calc(100vh - 70px);
      min-width: 350px;
      background-color: #36404A;
      border-right: 2px solid #A2ACBF;
      transition: all .5s;

      .block-change-statistics {
        display: flex;
        align-content: center;
        justify-content: space-evenly;
        align-items: center;
        font-weight: bold;
        font-size: 26px;
        color: #A2ACBF;
        border-bottom: 2px solid #A2ACBF;
        padding: 10px 0;

        .img-document {
          display: flex;

          img {
            width: 23px;
            height: 30px;
            margin-right: 7px;
          }
        }

        .change, .statistics {
          background: transparent;
          padding: 5px 10px;
          border-radius: 3px;
          font-weight: bold;
          font-size: 14px;
          outline: none;

          &:hover {
            color: #fff;
            cursor: pointer;
            transition: all .3s;
          }
        }

        .change {
          border: 1px solid $colorBtnChange;
          color: $colorBtnChange;

          &:hover {
            background-color: $colorBtnChange;
            box-shadow: 0 0 20px $colorBtnChange;
          }
        }

        .statistics {
          border: 1px solid $colorBtnStatistics;
          color: $colorBtnStatistics;

          &:hover {
            background-color: $colorBtnStatistics;
            box-shadow: 0 0 20px $colorBtnStatistics;
          }
        }
      }

      .highlight {
        color: #A2ACBF;
        border-bottom: 2px solid #A2ACBF;
        padding: 10px 0;
        font-weight: bold;
        font-size: 16px;
        text-transform: uppercase;
      }

      .colors {
        font-weight: bold;
        font-size: 18px;
        line-height: 21px;
        padding: 10px 0 10px 10px;
        border-bottom: 2px solid #A2ACBF;

        .default {
          background: transparent;
          padding: 5px 10px;
          border-radius: 3px;
          font-weight: bold;
          font-size: 14px;
          border: 1px solid $colorBtnDefault;
          color: $colorBtnDefault;

          &:hover {
            color: #fff;
            cursor: pointer;
            transition: all .3s;
            background-color: $colorBtnDefault;
            box-shadow: 0 0 20px $colorBtnDefault;
          }
        }
      }

      .changeTheme {
        border-bottom: 2px solid #A2ACBF;
        padding: 10px 0;

        .wrapper_toggle {
          display: flex;
          align-items: center;

          .toggle {
            width: 55px;
            height: 24px;
            border: 2px solid #0DFF92;
            box-sizing: border-box;
            border-radius: 36px;
            background-color: transparent;
            font-weight: bold;
            font-size: 11px;
            color: #0DFF92;
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-left: 10px;
            cursor: pointer;

            .toggle_title {
              margin-left: 5px;
              transition: all .3s;
            }

            .toggle_circle {
              width: 16px;
              height: 16px;
              background: #0DFF92;
              border-radius: 36px;
              transition: all .3s;
              margin-right: 2px;
            }
          }

          .darkTheme {
            margin-left: 10px;
            color: #0DFF92;
            transition: all .3s;
          }
        }
      }

      .block_hide {
        padding: 10px 0 0 10px;

        .hide {
          background: transparent;
          padding: 5px 10px;
          border-radius: 3px;
          font-weight: bold;
          font-size: 14px;
          border: 1px solid $colorBtnHide;
          color: $colorBtnHide;

          &:hover {
            color: #fff;
            cursor: pointer;
            transition: all .3s;
            background-color: $colorBtnHide;
            box-shadow: 0 0 20px $colorBtnHide;
          }
        }
      }
    }

    .openLeftSide {
      background-color: #4e5c6a;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      transition: .3s;
      cursor: pointer;
      position: fixed;
      height: 100%;
      z-index: 10;
      
      &:hover {
        background-color: #60758b;
      }

      .arrow {
        width: 20px;
        height: 20px;
      }
    }

    .rightSide {
      position: relative;
      padding: 10px 10px 80px 10px;
      width: 100%;
      transition: all .5s;

      .text {
        font-size: 23px;
      }
    }
  }

  /* Large Devices, Wide Screens */
  @media only screen and (max-width: 1200px) {
    /* */
  }

  /* Medium Devices, Desktops */
  @media only screen and (max-width: 992px) {
    /* */
  }

  /* Small Devices, Tablets */
  @media only screen and (max-width: 768px) {
    /* */
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    .main {
      width: 100%;
      line-height: 28px;
      overflow-x:hidden;

      .leftSide {
        min-width: 280px;

        .block-change-statistics {
          font-size: 18px;
          padding: 10px 0;

          .img-document {

            img {
              width: 20px;
              height: 27px;
              margin-right: 7px;
            }
          }

          .change, .statistics {
            padding: 5px 10px;
            border-radius: 3px;
            font-size: 12px;
          }
        }

        .highlight {
          padding: 10px 0;
          font-size: 14px;
        }

        .colors {
          font-size: 15px;
          padding: 10px 0 10px 10px;

          .default {
            padding: 5px 10px;
            border-radius: 3px;
            font-size: 12px;
          }
        }

        .block_hide {
          padding: 10px 0 0 10px;

          .hide {
            padding: 5px 10px;
            font-size: 12px;
          }
        }
      }

      .openLeftSide {
        height: 100%;

        .arrow {
          width: 20px;
          height: 20px;
        }
      }

      .rightSide {
        padding: 10px 10px 80px 10px;
        width: 100%;

        .text {
          font-size: 18px;
        }
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>