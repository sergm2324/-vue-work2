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
        <textarea id="value" rows="3" v-model="value"></textarea>
      </div>

      <button type="submit" class="btn primary">Добавить</button>
    </form>

    <div class="card card-w70">
<!--      <h1>Резюме Nickname</h1>-->
<!--      <div class="avatar">-->
<!--        <img src="https://cdn.dribbble.com/users/5592443/screenshots/14279501/drbl_pop_r_m_rick_4x.png">-->
<!--      </div>-->
<!--      <h2>Опыт работы</h2>-->
<!--      <p>-->
<!--        главный герой американского мультсериала «Рик и Морти», гениальный учёный, изобретатель, атеист (хотя в некоторых сериях он даже молится Богу, однако, каждый раз после чудесного спасения ссылается на удачу и вновь отвергает его существование), алкоголик, социопат, дедушка Морти. На момент начала третьего сезона ему 70 лет[1]. Рик боится пиратов, а его главной слабостью является некий - "Санчезиум". Исходя из того, что существует неограниченное количество вселенных, существует неограниченное количество Риков, герой сериала предположительно принадлежит к измерению С-137. В серии комикcов Рик относится к измерению C-132, а в игре «Pocket Mortys» — к измерению C-123[2]. Прототипом Рика Санчеза является Эмметт Браун, герой кинотрилогии «Назад в будущее»[3].-->
<!--      </p>-->
<!--      <h3>Добавьте первый блок, чтобы увидеть результат</h3>-->
      <span v-for = "(block, idx) in result" :key="idx" v-if = "result.length !==0">
        <component :is="nameComponent(block.type)"></component>
      </span>
      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <div class="container">
    <p v-if="!loading && comments.length === 0">
      <button class="btn primary" @click="fetchComments">Загрузить комментарии</button>
    </p>
    <div class="loader" v-if="loading"></div>
    <app-comments :comments = 'comments'></app-comments>
  </div>
</template>

<script>
import AppComments from '@/AppComments'
import AppTitle from '@/AppTitle'
import AppSubTitle from '@/AppSubTitle'
import AppAvatar from '@/AppAvatar'
import AppText from '@/AppText'
import axios from 'axios'
export default {
  data() {
    return {
      loading: false,
      comments: [],
      type: 'title',
      value: '',
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
      this.result.push({type: type, value: value})
      //добавляем в правую часть и пишем в базу type, value
    },
    nameComponent(type) {
      return this.names.find(item => item.type === type).component
    }
  },
  computed: {
  },
  components: {AppComments, AppTitle, AppSubTitle, AppAvatar, AppText }
}
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: auto;
    border-radius: 50%;
  }
</style>
