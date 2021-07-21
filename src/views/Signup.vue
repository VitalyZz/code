<template>
  <div class="main">
    <div class="title">Регистрация</div>
    <form @submit.prevent="submitHandler">
      <label for="email">Почта</label>
      <input type="text" id="email" name="email" class="email" v-model="email" autocomplete="off">

      <label for="password">Пароль</label>
      <input type="password" id="password" name="password" class="password" v-model="password">

      <label for="password_confirm">Повторите пароль</label>
      <input type="password" id="password_confirm" name="password_confirm" class="password_confirm" v-model="passwordConfirm">

      <div class="wrapper-btn">
        <button type="submit" class="btnSignup">Зарегистрироваться</button>
      </div>
    </form>

    <router-link to="/login" class="login">Войти</router-link>

    <div class="errors" v-if="errors.length !== 0">
      <div class="message" v-for="(error, index) in errors" :key="index">{{ index + 1 }}. {{ error.message }}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'signup',
  data() {
    return {
      email: '',
      password: '',
      passwordConfirm: '',
      errors: []
    }
  },
  methods: {
    async submitHandler() {
      this.errors = [];

      if (this.email.length < 5) this.errors.push({ message: 'Email не должен быть меньше 5 символов' });
      if (!/.+@.+/.test(this.email)) this.errors.push({ message: 'Email должен быть валидным' });
      if ((await axios.post('/checkEmail', { email: this.email })).data) this.errors.push({ message: 'Пользователь с таким email уже существует' });
      if (this.password.length < 4) this.errors.push({ message: 'Пароль не должен быть меньше 4 символов' });
      if (this.password !== this.passwordConfirm) this.errors.push({ message: 'Пароли не совпадают' });

      console.log('this.eerererere', this.errors);
      console.log('this.eerererere', this.errors);

      if (this.errors.length === 0) {
        const formData = {
          email: this.email,
          password: this.password,
          password_confirm: this.passwordConfirm
        }

        console.log('formData', formData)

        await this.$store.dispatch('auth/signup', formData);
        await this.$router.push('/');
      }
    }
  }
}
</script>

<style scoped lang="scss">
  .main {
    position: relative;
    width: 400px;
    height: 450px;
    border: 5px solid #0DFF92;
    border-radius: 3px;

    .errors {
      position: absolute;
      top: 0;
      right: -430px;
      padding: 10px 20px;
      background-color: #f003;
      color: #ff5d5d;
      font-size: 16px;
      font-weight: bold;
      border-radius: 3px;
      border: 2px solid #f00;

      .message {

        &:not(:last-child) {
          margin-bottom: 5px;
        }
      }
    }

    .title {
      font-weight: bold;
      font-size: 23px;
      color: #36404A;
      width: 100%;
      height: 60px;
      background: #0DFF92;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    form {
      display: flex;
      flex-direction: column;
      padding: 20px 40px;

      label {
        font-weight: bold;
        font-size: 18px;
        line-height: 21px;
        color: #0DFF92;
        margin-left: 15px;
        margin-bottom: 5px;
      }

      input {
        border: 3px solid #0DFF92;
        border-radius: 100px;
        padding: 10px 15px;
        background-color: transparent;
        margin-bottom: 15px;
        outline: none;
        font-weight: bold;
        font-size: 18px;
        color: #0DFF92;
      }

      .wrapper-btn {
        margin: 15px auto 0;

        .btnSignup {
          background: #0DFF92;
          border-radius: 30px;
          font-weight: bold;
          font-size: 18px;
          color: #36404A;
          border: none;
          padding: 15px 25px;
          cursor: pointer;
        }
      }
    }

    .login {
      font-weight: bold;
      font-size: 15px;
      color: #0DFF92;
      position: absolute;
      bottom: 10px;
      left: 10px;
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
    .main {
      .errors {
        top: auto;
        right: auto;
        bottom: -120px;
        left: 0;
        font-size: 14px;
      }
    }
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    .main {
      width: 280px;
      height: 390px;

      .errors {
        top: auto;
        right: auto;
        bottom: -120px;
        left: 0;
        padding: 5px 10px;
        font-size: 13px;

        .message {

          &:not(:last-child) {
            margin-bottom: 5px;
          }
        }
      }

      .title {
        font-size: 20px;
        width: 100%;
        height: 50px;
      }

      form {
        padding: 20px;

        label {
          font-size: 14px;
          margin-left: 15px;
          margin-bottom: 3px;
        }

        input {
          padding: 8px 15px;
          margin-bottom: 15px;
          font-size: 16px;
        }

        .wrapper-btn {
          margin: 5px auto 0;

          .btnSignup {
            border-radius: 30px;
            font-size: 16px;
            padding: 10px 25px;
          }
        }
      }

      .login {
        font-size: 15px;
        bottom: 10px;
        left: 10px;
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>