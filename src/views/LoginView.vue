<template>
  <div class="login">
    <div class="row justify-content-center">
      <h1 class="h3 mb-3 font-weight-normal">請先登入</h1>
      <div class="col-8">
        <form id="form" class="form-signin">
          <div class="form-floating mb-3">
            <input type="email" class="form-control" id="username" v-model="user.username" placeholder="name@example.com"
              required autofocus />
            <label for="username">Email address</label>
          </div>
          <div class="form-floating">
            <input type="password" class="form-control" id="password" v-model="user.password" placeholder="Password"
              required />
            <label for="password">Password</label>
          </div>
          <button class="btn btn-lg btn-primary w-100 mt-3" @click.prevent="login" type="submit">
            登入
          </button>
        </form>
      </div>
    </div>
    <p class="mt-5 mb-3 text-muted">&copy; 2021~∞ - 六角學院</p>
  </div>
</template>

<script>
import Swal from 'sweetalert2'
import axios from 'axios'
import router from '../router'

export default {
  name: 'LoginPage',
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      const api = `${import.meta.env.VITE_APP_API_URL}/admin/signin`
      axios
        .post(api, this.user)
        .then((res) => {
          const { token, expired } = res.data
          Swal.fire({
            title: '登入成功',
            text: '歡迎您回來',
            icon: 'success'
          }).then(() => {
            // 將 token 與 expired 設定好到期時間存入cookie
            document.cookie = `hexToken=${token}; expired=${new Date(
              expired
            )}; path=/`
            // 登入成功後轉址到產品頁面
            router.push({ name: 'Products' })
          })
        })
        .catch((err) => {
          console.dir(err)
          Swal.fire({
            title: `${err.data.message}`,
            text: '請檢察您的帳號密碼',
            icon: 'error'
          })
        })
    }
  },
  computed: {}
}
</script>

<style>

.login {
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.form-signin {
  width: 100%;
  max-width: 330px;
  padding: 15px;
  margin: auto;
}
</style>
