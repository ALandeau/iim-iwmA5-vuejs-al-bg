<template>
  <div id="root" class="columns">
    <div class="column is-one-quarter">
      <aside class="menu">
        <p class="menu-label">
          Line management
        </p>
        <ul class="menu-list">
          <li><a class="navbar-item" v-on:click="newData">Add new</a></li>
        </ul>
      </aside>
    </div>
    <div class="column">
      <article v-if="posts && posts.length">
        <div class="card" v-for="post of posts" :key="post.id">
          <header class="card-header">
            <p class="card-header-title">
              {{ post.name }}
            </p>
          </header>
          <div class="card-content">
            <div class="content">
              {{post.departure[0].name}} -> {{post.arrived[0].name}}
            </div>
          </div>
          <footer class="card-footer">
            <a href="#" class="card-footer-item" v-on:click="updateData(post.id)">Update</a>
            <a href="#" class="card-footer-item" v-on:click="deleteData(post.id)">Delete</a>
          </footer>
        </div>
      </article>
    </div>
  </div>
</template>

<script>
import axios from '~/plugins/axios'

const apiUrl = "http://localhost:3001/"

export default {
  data() {
    return {
      posts: [],
      destination: [],
      errors: []
    }
  },

  created() {
    axios.get(apiUrl + "destination")
      .then(response => {
        this.destination = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    axios.get(apiUrl + "line?_embed=schedule")
      .then(response => {
        let destination = this.destination
        this.posts = response.data.map(function(obj){
          obj.departure = destination.filter(function( o ) {
            return o.id == obj.departure;
          });
          obj.arrived = destination.filter(function( o ) {
            return o.id == obj.arrived;
          });
          return obj
        })
      })
      .catch(e => {
        this.errors.push(e)
      })
  },
  methods: {
    deleteData: function (id) {
      axios.delete(apiUrl + "line/" + id)
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

      axios.post(apiUrl + "line/", newPost)
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

      axios.patch(apiUrl + "line/" + id, updateData)
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
