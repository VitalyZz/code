<template>
  <div v-if="showLoader" class="wrapperLoader">
    <Loader/>
  </div>

  <div v-if="!showLoader" class="topPanel">
    <button class="deleteTheList" @click="deleteWords" :disabled="checked.length === 0"><img :src="img_trash_bin" alt=""></button>
    <button class="changeTheDictionary" @mousedown="dropDownMenuHandler" :disabled="checked.length === 0"><img :src="img_files" alt=""></button>
  </div>

  <table v-if="!showLoader">
    <tr>
      <th>Выбор</th>
      <th>Слово</th>
      <th>Перевод</th>
      <th>Правильные</th>
      <th>Неправильные</th>
      <th>Словарь</th>
    </tr>
    <tr v-for="(word, index) in words" :key="word.id_word_information">
      <DictionaryWord
          :word="word"
          :index="index"
          v-model="checked"
      />
    </tr>
  </table>

  <div class="wrapperCard" v-if="!showLoader">
    <div class="card"
     v-for="(word, index) in words" :key="word.id_word_information"
    >
      <DictionaryWordCard
          :word="word"
          :index="index"
          v-model="checked"
      />
    </div>
  </div>

  <teleport to="body">
    <DropDownMenu
      v-if="showDropDownMenu"
      :x="x"
      :y="y"
      :dictionaries="dictionaries"
      @selectDictionary="changeDictionary"
    />
  </teleport>
</template>

<script>
import axios from "axios";
import DictionaryWord from "@/components/dictionaries/DictionaryWord";
import DictionaryWordCard from "@/components/dictionaries/DictionaryWordCard";
import Loader from "@/components/app/Loader";
import DropDownMenu from "@/components/dictionaries/DropDownMenu";
import img_trash_bin from '@/assets/images/trash-bin.svg';
import img_files from '@/assets/images/files.svg';

export default {
  data() {
    return {
      img_trash_bin,
      img_files,
      words: [],
      dictionaries: [],
      showLoader: true,
      checked: [],
      showDropDownMenu: false,
      currentDictionary: {},
      x: 0,
      y: 0
    }
  },
  methods: {
    closeDropDownMenu () {
      this.showDropDownMenu = false
    },
    dropDownMenuHandler(event) {
      const values = event.target.getBoundingClientRect();

      this.x = values.left + values.width / 2;
      this.y = values.top + values.height + 5;

      this.showDropDownMenu = true
    },
    async deleteWords() {
      const id_word_information = Object.values(this.checked);

      const id_dictionary = this.$route.params.id;
      const id_user = this.$store.getters['auth/getCurrentUser'].id;

      this.words = this.words.filter(el => id_word_information.indexOf(el.id_word_information) === -1)

      if (['known', 'study'].indexOf(id_dictionary) !== -1) {
        await axios.post('/words/deleteWithoutDictionaryById', { id_user, id_word_information })
      } else {
        await axios.post('/words/delete', { id_user, id_dictionary, id_word_information })
      }

      this.checked = [];
    },
    async changeDictionary(id_dictionary, title) {
      const id_word_information = Object.values(this.checked);
      const id_user = this.$store.getters['auth/getCurrentUser'].id;
      const id_page = this.$route.params.id;
      this.closeDropDownMenu();

      if (['known', 'study'].indexOf(id_page) !== -1) {
        this.words.map(el => {
          if (id_word_information.indexOf(el.id_word_information) !== -1) {
            el.title = title;
            el.id_dictionary = id_dictionary;
          }
        })
      } else {
        if (this.currentDictionary.title !== title) {
          this.words = this.words.filter(el => {
            if (el.title !== title) {
              return id_word_information.indexOf(el.id_word_information) === -1
            }
          })
        }
      }

      await axios.post('/word/updateByChangeDictionary', { id_user, id_word_information, id_dictionary })

      this.checked = [];
    }
  },
  async mounted() {
    const id_user = this.$store.getters['auth/getCurrentUser'].id;
    const id_dictionary = this.$route.params.id;

    this.dictionaries = (await axios.post('/dictionary/getAll', {id_user})).data

    if (['known', 'study'].indexOf(id_dictionary) !== -1) {
      const values = {
        'study': 1,
        'known': 0,
      }
      const data = {
        id_user,
        meaning: values[this.$route.params.id]
      }
      this.words = (await axios.post('/words/getByMeaningWithTranslations', data)).data
    } else {
      const data = {
        id_user,
        id_dictionary
      }
      this.currentDictionary = (await axios.post('/dictionary/getDictionaryById', { id_user, id_dictionary })).data
      this.words = (await axios.post('/words/getByDictionaryId', data)).data
    }

    this.showLoader = false
  },
  components: {
    DictionaryWord,
    Loader,
    DropDownMenu,
    DictionaryWordCard
  }
}
</script>

<style scoped lang="scss">
  .topPanel {
    position: absolute;
    left: 30px;
    top: 100px;

    .deleteTheList, .changeTheDictionary {
      border-radius: 3px;
      font-weight: bold;
      font-size: 16px;
      cursor: pointer;
      transition: .3s;
      color: #fff;
      border: none;
      padding: 5px;

      &:disabled {
        background-color: #8d8d8d;

        &:hover {
          box-shadow: none;
        }
      }
    }

    .deleteTheList {
      margin-right: 20px;
      background-color: #ff4646;

      img {
        width: 20px;
        height: 20px;
      }

      &:hover {
        box-shadow: 0 0 25px 1px #ff4646;
      }
    }

    .changeTheDictionary {
      background-color: #06b8ff;

      img {
        width: 20px;
        height: 20px;
      }

      &:hover {
        box-shadow: 0 0 25px 1px #06b8ff;
      }
    }
  }

  .wrapperCard {
    display: none;
    margin-top: 70px;
    padding: 10px;

    .card {
      margin-bottom: 10px;
      background-color: #485d73;
      padding: 10px;
      box-shadow: 5px 5px 10px -2px #485d73;
      word-break: break-all;
    }
  }

  table {
    margin-top: 70px;
    border-spacing: 0;
    padding: 10px;
    tr {
      th {
        color: #fff;
        text-align: center;
        font-size: 18px;
        padding: 10px;
        border-bottom: 2px solid #0DFF92;
      }
    }
  }

  h1 {
    margin: 20px 0;
    color: #fff;
  }

  .wrapperLoader {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .main {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
  }

  /* 1400 */
  @media only screen and (max-width: 1400px) {
    .topPanel {
      left: 30px;
      top: 100px;
    }

    table {
      tr {
        th {
          font-size: 15px;
          padding: 5px;
        }
      }
    }
  }

  /* Large Devices, Wide Screens */
  @media only screen and (max-width: 1200px) {
    table {
      display: none;
    }

    .wrapperCard {
      display: block;
    }
  }

  /* Medium Devices, Desktops */
  @media only screen and (max-width: 992px) {
    table {
      margin-top: 70px;
      tr {
        th {
          font-size: 16px;
          padding: 10px;
        }
      }
    }
  }

  /* Small Devices, Tablets */
  @media only screen and (max-width: 768px) {

  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    /* */
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>