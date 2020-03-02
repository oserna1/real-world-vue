<template>
  <div>
    <h1>Events for {{ user.user.name }}</h1>
    <EventCard v-for="event in event.events" :key="event.id" :event="event" />
    <template v-if="page != 1">
      <router-link
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        >Prev Page</router-link
      >
      |
    </template>
    <template v-if="page * getEventsPerPage < getTotalEvents">
      <router-link
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        >Next Page</router-link
      >
    </template>
  </div>
</template>

<script>
import EventCard from '../components/EventCard.vue'
import { mapState, mapGetters } from 'vuex'

export default {
  components: {
    EventCard
  },
  created() {
    this.$store.dispatch('event/fetchEvents', {
      perPage: 3,
      page: this.page
    })
  },
  computed: {
    page() {
      return parseInt(this.$route.query.page) || 1
    },
    // using getters with namespacing and withouth mapGetters
    // numOfEvents() {
    //   return this.$store.getters['event/getTotalEvents']
    // },
    // perPage() {
    //   return this.$store.getters['event/getEventsPerPage']
    // },
    ...mapGetters('event', ['getEventsPerPage', 'getTotalEvents']),
    ...mapState(['event', 'user'])
  }
}
</script>
