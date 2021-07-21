<template>
  <div class="btn_block">
    <button class="create-text" @click="showModalAddText = true">Добавить новый текст</button>
  </div>

  <div v-if="showLoader" class="wrapperLoader">
    <Loader/>
  </div>

  <div class="texts_block" v-else>
    <TextBlock
      v-for="text in texts"
      :key="text.id_text"
      :title="text.title"
      :text="text.text"
      :id_user="text.id_user"
      :id_text="text.id_text"
      :publicAccess="text.public"
      :deleteTextBool="text.delete"
      @deleteText="deleteText"
    />
  </div>

  <teleport to="body">
    <ModalAddText
        v-if="showModalAddText"
        @closeModalAddText="showModalAddText = false"
        @addText="addText"
    />
  </teleport>
</template>

<script>
import TextBlock from "@/components/texts/TextBlock";
import ModalAddText from "@/components/texts/ModalAddText";
import Loader from "@/components/app/Loader";
import axios from "axios";

export default {
  name: 'texts',
  data() {
    return {
      texts: [],
      showModalAddText: false,
      showLoader: true
    }
  },
  methods: {
    async deleteText(id_user, id_text) {
      this.texts = this.texts.filter(el => el.id_text !==  id_text)
      await axios.post('/text/delete', { id_user, id_text })
    },
    async addText(title, text) {
      if (title.length >= 3 && text.length >= 5) {
        const id_user = this.$store.getters['auth/getCurrentUser'].id;

        const formData = { title, text, id_user, public: 0 };

        this.showModalAddText = false;
        this.texts.unshift({ ...(await axios.post('/text/create', formData)).data, delete: true });

        console.log(this.texts);
      }
    }
  },
  async mounted() {
    this.showLoader = true

    // const check = (await axios.get('/check', {
    //   withCredentials: true
    // })).data
    //
    // console.log('Check mounted TEXTS', check);

    // const data = (await axios('/check', {
    //   withCredentials: true,
    // })).data;
    //
    // console.log('CHEEEEEEEECKKK tttttt:', data)

    // get translates

    // const word = 'Conquered'
    // const url = `https://owlbot.info/api/v4/dictionary/${word}`;
    // const token = "073b418a9db1fcde565807b88a9f2ad25a30cd25";
    //
    // const params = {
    //   method: 'GET',
    //   headers: {
    //     'Authorization': 'Token ' + token
    //   }
    // }
    // const data = await fetch(url, params);
    // const result = await data.json();
    //
    // console.log('owlbot info', result);

    // get translates

    // get yandex START

    // const key = "dict.1.1.20210601T151729Z.cb8f7f225a387d74.67724ff1c9c838097dba9aace6250395f85202d2";
    // const words = (await axios.get('/words/getAll')).data
    //
    // for (const el of words) {
    //   const url = `https://dictionary.yandex.net/api/v1/dicservice.json/lookup?key=${key}&lang=en-ru&text=${el.word}`;
    //   const data = await fetch(url);
    //   const result = await data.json();
    //
    //   let translations = [];
    //
    //   result.def.forEach(el => {
    //     el.tr.forEach(el2 => {
    //       translations.push(el2.text)
    //     })
    //   })
    //
    //   translations = translations.slice(0, 4);
    //
    //   for (const translation of translations) {
    //     await axios.post('/words/insertTranslation', {translation, id_word: el.id_word})
    //   }
    // }

    // get yandex END

    const id_user = this.$store.getters['auth/getCurrentUser'].id;
    const response = await axios.post('/texts/getAll', { id_user })
    this.texts = response.data
    this.texts.push(...(await axios.get('texts/getPublic')).data)
    this.texts.map(el => {
      if (el.id_user === id_user) {
        el.delete = true;
      }
      else el.delete = !(el.public === '1' && el.id_user !== id_user);
    })

    // Парсинг текста [START]



    // Парсинг текста [END]

    this.showLoader = false
  },
  components: {
    TextBlock,
    ModalAddText,
    Loader
  }
}
</script>

<style scoped lang="scss">
  .btn_block {
    margin-top: 30px;

    button.create-text {
      display: block;
      padding: 5px 10px;
      border: 2px solid #0DFF92;
      background-color: transparent;
      color: #0DFF92;
      font-weight: bold;
      font-size: 16px;
      cursor: pointer;
      border-radius: 2px;
      transition: .3s;

      &:hover {
        background-color: #0DFF92;
        color: #36404A;
      }
    }
  }

  .wrapperLoader {
    display: flex;
    justify-content: center;
  }

  .texts_block {
    margin-bottom: 40px;
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
    .btn_block {
      button.create-text {
        font-size: 14px;
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>