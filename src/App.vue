<template>
  <div class="container">
    <app-alert :alert="alert" @close="alert = null"></app-alert>
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

        <button type="submit" :disabled = "disabledSubmit" class="btn primary">Добавить</button>
      </form>

      <div class="card card-w70">
      <span v-for = "block in result" :key="block.id" v-if = "result.length !==0" class="inline">
        <component :is="nameComponent(block.type)" v-bind="{ value: block.value }"></component>
        <button class="btn danger" @click="deleteElement(block.id)">Удалить</button>
      </span>
        <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
      </div>
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
import AppAlert from '@/AppAlert'
import axios from 'axios'
export default {
  data() {
    return {
      loading: false,
      comments: [],
      type: 'title',
      value: '',
      alert: null,
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
  mounted() {
    this.loadElements()
  },
  methods: {
    async fetchComments() {
      this.loading = true
      const {data} = await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42')
      this.comments = data
      this.loading = false
    },
    async addElement(type, value) {
      const response = await fetch('https://vue-resume-8b93b-default-rtdb.firebaseio.com/element.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          type: type,
          value: type === 'avatar' ? this.defaultAvatarValue : value
        })
      })
      const firebaseData = await response.json()

      type === 'avatar' ? this.result.push({id: firebaseData.name, type: type, value: this.defaultAvatarValue}) : this.result.push({id: firebaseData.name, type: type, value: value})
      this.value = ''
      this.type = 'title'
    },
    async loadElements() {
      try {
        this.loading = true
        const {data} = await axios.get('https://vue-resume-8b93b-default-rtdb.firebaseio.com/element.json')
        if (!data) {
          throw new Error('Список элементов пуст')
        }
        this.result = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.loading = false
      }catch (e) {
        this.alert = {
          type: 'danger',
          title: 'Ошибка',
          text: e.message
        }
        this.loading = false
      }

    },
    nameComponent(type) {
      return this.names.find(item => item.type === type).component
    },
    async deleteElement(id) {
      try {
        const type = this.result.find(element => element.id === id).type
        await axios.delete(`https://vue-resume-8b93b-default-rtdb.firebaseio.com/element/${id}.json`)
        this.result = this.result.filter(element => element.id !== id)
        this. alert = {
          type: 'primary',
          title: 'Успешно',
          text: `Элемент с типом "${type}" был удален`
        }
      }catch (e) {

      }
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
    },
    disabledSubmit() {
      return this.type === 'avatar' ? this.defaultAvatarValue.length === 0 : this.value.length < 4
    }
  },
  components: {AppComments, AppTitle, AppSubTitle, AppAvatar, AppText, AppLoader, AppAlert }
}
</script>

<style scoped>

.inline{
  display: flex;
  justify-content: space-between;
  align-items: center;
}

</style>
