<template>
  <div id="root">
    <h1>IIM IWM A5 VueJS - AL - BG</h1>
    <section class="container">
      <button v-on:click="newData">New post</button>
      <article v-if="posts && posts.length">
        <div v-for="post of posts" :key="post.id">
          <h2>{{post.title}}</h2>
          <p>{{post.content}}</p>
          <p class="font-italic">{{post.author}}</p>
          <button v-on:click="updateData(post.id)">Update</button>
          <button v-on:click="deleteData(post.id)">Delete</button>
        </div>
      </article>
    </section>
  </div>
</template>

<script>
import axios from '~/plugins/axios'

const apiUrl = "http://localhost:3001/posts/"

export default {
  data() {
    return {
      posts: [],
      errors: []
    }
  },

  created() {
    axios.get(apiUrl)
      .then(response => {
        this.posts = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
  },
  methods: {
    deleteData: function (id) {
      axios.delete(apiUrl + id)
        .then(response => {
          this.posts = this.posts.filter(function( obj ) { 
            return obj.id !== id;
          });
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    newData: function() {
      let newPost = {
        "title": "New Article",
        "author": "John Doe",
        "content": "Content of a new article"
      }

      axios.post(apiUrl, newPost)
       .then(response => {
         this.posts.push(response.data)
       })
       .catch(e => {
         this.errors.push(e)
       })
    },
    updateData: function(id) {
      let updateData = {
        "author": "John Updated",
      }
      
      axios.patch(apiUrl + id, updateData)
        .then(response => {
          this.posts = this.posts.map(function( obj ) { 
            return obj.id == id ? response.data : obj
          });
        })
        .catch(e => {
          this.errors.push(e)
        })
    }
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
