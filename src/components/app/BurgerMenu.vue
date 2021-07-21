<template>
  <div class="main" v-if="isShow">
    <ul v-if="getAuth">
<!--      <router-link-->
<!--          v-for="link in links"-->
<!--          :key="link.url"-->
<!--          :to="link.url"-->
<!--          custom-->
<!--          v-slot="{ isActive, href, navigate }">-->
<!--        <li :class="isActive ? 'active' : ''">-->
<!--          <a :href="href" @click="navigate; $emit('closeBurgerMenu')">{{ link.title }}</a>-->
<!--        </li>-->
<!--      </router-link>-->
      <router-link
          v-for="link in links"
          :key="link.url"
          :to="link.url"
          custom
          v-slot="{ isActive, href, navigate }">
        <li :class="isActive ? 'active' : ''">
          <a :href="href" @click="navigate; $emit('closeBurgerMenu')">{{ link.title }}</a>
        </li>
      </router-link>

      <router-link
          v-if="getCurrentUser.role === 'ADMIN'"
          to="/admin"
          custom
          v-slot="{ isActive, href, navigate }">
        <li :class="isActive ? 'active' : ''">
          <a :href="href" @click="navigate; $emit('closeBurgerMenu')">Админ</a>
        </li>
      </router-link>
    </ul>

    <div class="end" v-if="getAuth">
      <ul>
        <li>
          <a @click="logout" class="logout">Выход</a>
        </li>
      </ul>
    </div>

    <div class="notAuth" v-else>
      <ul>
        <li><router-link to="/login" class="login" @click="$emit('closeBurgerMenu')">Авторизация</router-link></li>
        <li><router-link to="/signup" class="signup" @click="$emit('closeBurgerMenu')">Регистрация</router-link></li>
      </ul>
    </div>

    <div @click="$emit('closeBurgerMenu')" class="close"></div>
  </div>

  <div class="dark"></div>
</template>

<script>
import {mapGetters} from "vuex";

export default {
  emits: ['closeBurgerMenu'],
  data() {
    return {
      isLogin: false,
      isShow: false,
      links: [
        {title: 'Тексты', url: '/texts'},
        {title: 'Словари', url: '/dictionaries'},
        {title: 'Статистика', url: '/statistics'},
        {title: 'Тренажеры', url: '/simulators'},
      ],
    }
  },
  computed: {
    ...mapGetters('auth', ['getCurrentUser', 'getAuth'])
  },
  mounted() {
    const checkAuth = this.getAuth;

    if (checkAuth) this.isLogin = true;
    this.isShow = true;
  },
  methods: {
    logout() {
      this.$store.dispatch('auth/logout');
      this.$router.push('/login');
      this.$emit('closeBurgerMenu')
    },
    burgerMenuHandler() {
      this.burgerMenu = !this.burgerMenu;
      document.querySelector('body').style.height = this.burgerMenu ? '100vh' : '100%';
      document.querySelector('body').style.overflow = this.burgerMenu ? 'hidden' : 'auto';
    }
  },
}
</script>

<style scoped lang="scss">
  .main {
    display: flex;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #36404ad4;
    z-index: 15;
    color: #fff;
    flex-direction: column;
    justify-content: center;
    font-size: 23px;
    border-radius: 5px;
    width: 100%;
    height: 100%;

    ul {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;
      list-style: none;
      padding: 0;
      text-align: center;
      li {
        width: 100%;
        display: flex;
        padding: 10px;

        a {
          font-weight: bold;
          font-size: 23px;
          color: #fff;
          width: 100%;
          background-color: rgba(13, 255, 146, 0.5);
          padding: 10px;
        }
      }
    }

    .logout {
      a {
        font-weight: bold;
        font-size: 23px;
        color: #fff;
        width: 100%;
        background-color: rgba(13, 255, 146, 0.5);
        padding: 10px;
      }
    }

    .element {
      display: flex;
      align-items: center;
      margin-right: 5px;
    }

    .close {
      position: absolute;
      color: #fff;
      top: 0;
      right: 0;
      cursor: pointer;
      width: 30px;
      height: 30px;
      padding: 6px;
      transition: all .3s;

      &:before, &:after {
        position: absolute;
        content: '';
        width: 3px;
        height: 30px;
        background-color: #fff;
        top: calc(50% - 15px);
        right: calc(50% - 2px);
      }

      &:before {
        transform: rotate(-45deg);
      }

      &:after {
        transform: rotate(45deg);
      }

      &:hover {
        border-color: #FF4D4D;
        transition: all .3s;
      }

      &:hover:after, &:hover:before {
        background-color: #fff;
        transition: all .3s;
      }
    }
  }

  .dark {
    display: block;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.8);
    position: absolute;
    top: 0;
    left: 0;
    z-index: 10;
  }
</style>