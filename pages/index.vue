<template>
  <div id="root">
    <h1>IIM IWM A5 VueJS - AL - BG</h1>
    <section class="container">
      <article v-if="posts && posts.length">
        <div v-for="post of posts" :key="post.id">
          <h2>{{post.title}}</h2>
          <p>{{post.content}}</p>
          <p class="font-italic">{{post.author}}</p>
        </div>
      </article>
    </section>
  </div>
</template>

<script>
import axios from '~/plugins/axios'

export default {
  data() {
    return {
      posts: []
    }
  },

  created() {
    axios.get(`http://localhost:3001/posts`)
      .then(response => {
        this.posts = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
  }
}
</script>

<style lang="scss">

 h1 {
   text-align: center;
 }

.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;

  article div {
    margin: 10px 0;
    padding: 10px 25px;
    background-color: rgba(33, 33, 33, 0.05);
    border: { radius: 5px }
    cursor: pointer;

    .font-italic {
      font-style: italic;
    }
  }
}
</style>
