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
          <header class="card-header" v-on:click="modalSchedule(post.id)">
            <p class="card-header-title">
              {{ post.name }}
            </p>
            <a href="#" class="card-header-icon" aria-label="more options">
              <span class="icon">
                <i :id="'icon-close-schedule'+post.id" class="fas fa-angle-down"></i>
                <i :id="'icon-open-schedule'+post.id" class="icon-open-schedule fas fa-angle-up"></i>
              </span>
            </a>
          </header>
          <div class="card-content">
            <div :id="'schedule-element' + post.id" class="schedule-box">
              <div v-for="schedule in post.schedule" :key="schedule.id">
                <div class="tabs is-small">
                  <ul>
                    <li v-if="day === currentDay" v-for="day in days" :key="day" class="is-active">
                      <a v-on:click="updateDay(day)" class="day-button">{{ day }}</a>
                    </li>
                    <li v-else>
                      <a v-on:click="updateDay(day)" class="day-button">{{ day }}</a>
                    </li>
                  </ul>
                </div>
                <schedule-component
                  :select-day="currentDay"
                  :schedule="schedule"/>
              </div>
            </div>
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
import ScheduleComponent from "../components/scheduleComponent";

const apiUrl = "http://localhost:3001/"

export default {
  components: {ScheduleComponent},
  data() {
    return {
      posts: [],
      destination: [],
      errors: [],
      days: ["monday", "tuesday", "wednesday", "thursday", "friday", "saturday", "sunday"],
      currentDay: "monday",
      show: false
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
          obj.departure = getDeparture(obj, destination)
          obj.arrived = getArrived(obj, destination)
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
        "name": "New line",
        "departure": 5,
        "arrived": 6
      }

      axios.post(apiUrl + "line", newPost)
        .then(response => {
          let destination = this.destination
          response.data.departure = getDeparture(response.data, destination)
          response.data.arrived = getArrived(response.data, destination)
          this.posts.push(response.data)
       })
       .catch(e => {
         this.errors.push(e)
       })
    },
    updateData: function(id) {
      let updateData = {
        "name": "Update line",
      }

      axios.patch(apiUrl + "line/" + id, updateData)
        .then(response => {
          let destination = this.destination
          response.data.departure = getDeparture(response.data, destination)
          response.data.arrived = getArrived(response.data, destination)
          this.posts = this.posts.map(function( obj ) {
            if (obj.id === id) {
              response.data.schedule = obj.schedule
              return response.data
            }
            return obj
          });
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    modalSchedule: function(id) {
      if (this.show === false) {
        this.show = true;
        document.getElementById('schedule-element'+id).style.display = "block";
        document.getElementById('icon-close-schedule'+id).style.display = "none";
        document.getElementById('icon-open-schedule'+id).style.display = "block"
      } else {
        this.show = false;
        document.getElementById('schedule-element'+id).style.display = "none";
        document.getElementById('icon-close-schedule'+id).style.display = "block";
        document.getElementById('icon-open-schedule'+id).style.display = "none"
      }
    },
    updateDay: function(day) {
      this.currentDay = day;
    }
  }
}

function getDeparture(obj, destination) {
  return destination.filter(function( o ) {
    return o.id == obj.departure;
  });
}

function getArrived(obj, destination) {
  return destination.filter(function( o ) {
    return o.id == obj.arrived;
  });
}

</script>

<style lang="scss">
  .card {
    .card-header{
      cursor: pointer;
    }
    .schedule-box {
      display: none;
    }
    .icon-open-schedule {
      display: none;
    }
    .day-button {
      text-transform: capitalize;
    }
    .schedule-content {
      margin: 10px 0 40px;
      .tag {
        margin: 0 2px;
      }
    }
  }
</style>
