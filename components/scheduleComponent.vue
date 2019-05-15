<template>
    <div class="schedule-content">
      <div v-if="selectDay==='monday'">
        <span v-for="time in schedule.monday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='tuesday'">
        <span v-for="time in schedule.tuesday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='wednesday'">
        <span v-for="time in schedule.wednesday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='thursday'">
        <span v-for="time in schedule.thursday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='friday'">
        <span v-for="time in schedule.friday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='saturday'">
        <span v-for="time in schedule.saturday" class="tag is-light">{{ time }}</span>
      </div>
      <div v-if="selectDay==='sunday'">
        <span v-for="time in schedule.sunday" class="tag is-light">{{ time }}</span>
      </div>
    </div>
</template>

<script>
    export default {
        name: "scheduleComponent",
        props: {
          selectDay: {
            type: String,
            required: true
          },
          schedule: {
            type: Object,
            required: true
          }
        }
    }
</script>

<style scoped>

</style>
