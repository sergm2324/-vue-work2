<template>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="addElement(type, value)">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="type">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" rows="3" v-model="typeValue"></textarea>
      </div>

      <button type="submit" class="btn primary">Добавить</button>
    </form>

    <div class="card card-w70">
      <span v-for = "(block, idx) in result" :key="idx" v-if = "result.length !==0">
        <component :is="nameComponent(block.type)" v-bind="{ value: block.value }"></component>
      </span>
      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <div class="container">
    <p v-if="!loading && comments.length === 0">
      <button class="btn primary" @click="fetchComments">Загрузить комментарии</button>
    </p>
    <app-loader v-if="loading"></app-loader>
    <app-comments :comments = 'comments'></app-comments>
  </div>
</template>

<script>
import AppComments from '@/AppComments'
import AppTitle from '@/AppTitle'
import AppSubTitle from '@/AppSubTitle'
import AppAvatar from '@/AppAvatar'
import AppText from '@/AppText'
import AppLoader from '@/AppLoader'
import axios from 'axios'
export default {
  data() {
    return {
      loading: false,
      comments: [],
      type: 'title',
      value: '',
      defaultAvatarValue: 'https://cdn.dribbble.com/users/5592443/screenshots/14279501/drbl_pop_r_m_rick_4x.png',
      result: [],
      names: [
        { type: 'title', component: 'AppTitle' },
        { type: 'subtitle', component: 'AppSubTitle' },
        { type: 'avatar', component: 'AppAvatar' },
        { type: 'text', component: 'AppText' }
      ]
    }
  },
  methods: {
    async fetchComments() {
      this.loading = true
      const {data} = await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42')
      this.comments = data
      this.loading = false
    },
    addElement(type, value) {
      type === 'avatar' ? this.result.push({type: type, value: this.defaultAvatarValue}) : this.result.push({type: type, value: value})
      this.value = ''
      //добавляем в правую часть и пишем в базу type, value
    },
    nameComponent(type) {
      return this.names.find(item => item.type === type).component
    }
  },
  computed: {
    typeValue: {
      // getter
      get: function () {
        return (this.type === 'avatar' ? this.defaultAvatarValue : this.value)
      },
      // setter
      set: function (newValue) {
        this.type === 'avatar' ? this.defaultAvatarValue = newValue : this.value = newValue
      }
    }
  },
  components: {AppComments, AppTitle, AppSubTitle, AppAvatar, AppText, AppLoader }
}
</script>
