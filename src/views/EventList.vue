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
import store from '../store'

function getPageEvents(routeTo, next) {
  const currentPage = parseInt(routeTo.query.page) || 1
  store
    .dispatch('event/fetchEvents', {
      page: currentPage
    })
    .then(() => {
      routeTo.params.page = currentPage
      next()
    })
}

export default {
  props: {
    page: {
      type: Number,
      required: true
    }
  },
  beforeRouteUpdate(routeTo, routeFrom, next) {
    getPageEvents(routeTo, next)
  },
  components: {
    EventCard
  },
  beforeRouteEnter(routeTo, routeFrom, next) {
    getPageEvents(routeTo, next)
  },
  computed: {
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
