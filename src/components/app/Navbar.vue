<template>
  <header v-if="isShow">
    <div class="start">
      <router-link to="/texts">English-lan</router-link>
    </div>
    <div class="center" v-if="getAuth">
      <ul class="navigation">
        <router-link
            v-for="link in links"
            :key="link.url"
            :to="link.url"
            custom
            v-slot="{ isActive, href, navigate }">
          <li :class="isActive ? 'active' : ''">
            <a :href="href" @click="navigate">{{ link.title }}</a>
          </li>
        </router-link>

        <router-link
            v-if="getCurrentUser.role === 'ADMIN'"
            to="/admin"
            custom
            v-slot="{ isActive, href, navigate }">
          <li :class="isActive ? 'active' : ''">
            <a :href="href" @click="navigate">Админ</a>
          </li>
        </router-link>
      </ul>
    </div>

    <div class="end" v-if="getAuth">
      <a @click="logout">Выход</a>
    </div>

    <div class="notAuth" v-else>
      <router-link to="/login" class="login">Авторизация</router-link>
      <router-link to="/signup" class="signup">Регистрация</router-link>
    </div>

    <div class="burger-menu" @click="burgerMenuHandler"></div>
    <teleport to="body">
      <BurgerMenu
          v-if="burgerMenu"
          @closeBurgerMenu="burgerMenuHandler"
      />
    </teleport>
  </header>
</template>

<script>
import BurgerMenu from "@/components/app/BurgerMenu";
import { mapGetters } from 'vuex'

export default {
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
      burgerMenu: false
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
    },
    burgerMenuHandler() {
      this.burgerMenu = !this.burgerMenu;
      document.querySelector('body').style.height = this.burgerMenu ? '100vh' : '100%';
      document.querySelector('body').style.overflow = this.burgerMenu ? 'hidden' : 'auto';
    }
  },
  components: {
    BurgerMenu
  }
}
</script>

<style scoped lang="scss">
  header {
    position: fixed;
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    height: 70px;
    background-color: #0DFF92;
    z-index: 10;
  }

  .center {
    .navigation {
      display: flex;
      align-items: center;
      justify-content: space-between;
      list-style: none;
      padding: 0;

      li:not(:last-child) {
        margin-right: 35px;
      }

      .active {
        color: #007dff;
        border-bottom: 2px solid #36404A;
      }

      li {
        border-bottom: 2px solid transparent;
        transition: .3s;

        a {
          font-weight: bold;
          font-size: 23px;
          color: #36404A;
        }

        &:hover {
          border-bottom: 2px solid #36404A;
          transition: .3s;
        }
      }
    }
  }

  .start {
    margin-left: 20px;
    a {
      font-family: Ubuntu, sans-serif;
      font-style: normal;
      font-weight: bold;
      font-size: 40px;
      color: #36404A;
    }
  }

  .end {
    margin-right: 20px;

    a {
      font-weight: bold;
      font-size: 23px;
      color: #36404A;
      transition: .3s;
      cursor: pointer;

      &:hover {
        color: #ff6363;
        transition: .3s;
      }
    }
  }

  .notAuth {
    margin-right: 20px;

    .login {
      margin-right: 10px;
    }

    a {
      font-weight: bold;
      font-size: 23px;
      color: #36404A;
      transition: .3s;
    }
  }

  .burger-menu {
    display: none;
    margin-right: 30px;
    background-color: #36404A;
    position: relative;
    width: 25px;
    height: 2px;

    &:before {
      content: '';
      top: 4px;
      position: absolute;
      background-color: #36404A;
      width: 100%;
      height: 2px;
    }

    &:after {
      content: '';
      top: -4px;
      position: absolute;
      background-color: #36404A;
      width: 100%;
      height: 2px;
    }
  }

  /* Large Devices, Wide Screens */
  @media only screen and (max-width: 1200px) {
    .start {
      a {
        font-size: 25px;
      }
    }

    .center {
      .navigation {
        li:not(:last-child) {
          margin-right: 25px;
        }

        li {
          a {
            font-weight: bold;
            font-size: 18px;
            color: #36404A;
          }
        }
      }
    }

    .end {
      a {
        font-size: 18px;
      }
    }
  }

  /* Medium Devices, Desktops */
  @media only screen and (max-width: 992px) {
    /* */
  }

  /* Small Devices, Tablets */
  @media only screen and (max-width: 768px) {
    .center, .end, .notAuth {
      display: none;
    }

    .burger-menu {
      display: block;
    }
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