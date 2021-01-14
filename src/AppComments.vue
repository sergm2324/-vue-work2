<template>
  <div class="card" v-if = "comments.length !== 0">
    <h2>Комментарии</h2>
    <ul class="list">
      <li class="list-item" v-for = "comment in comments" :key="comments.id">
        <div>
          <p><strong>{{comment.email}}</strong></p>
          <small>{{comment.body}}</small>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "AppComments",
  props: ['comments']
}
</script>

<style scoped>

</style>
