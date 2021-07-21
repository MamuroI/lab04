<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />

    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventList', query: { page: page - 1, perPage: perPage } }"
        rel="prev"
        v-if="page != 1"
      >
        Prev Page
      </router-link>
      <router-link
        id="page-next"
        :to="{ name: 'EventList', query: { page: page + 1, perPage: perPage } }"
        rel="next"
        v-if="hasNextPage"
      >
        Next Page
      </router-link>
    </div>
    <div class="pageLimit">
      <router-link
        id="page-decrease"
        :to="{ name: 'EventList', query: { page: page, perPage: perPage - 1 } }"
        rel="decrease"
        v-if="perPage > 1"
      >
        Decrease size
      </router-link>
      <router-link
        id="page-increase"
        :to="{ name: 'EventList', query: { page: page, perPage: perPage + 1 } }"
        rel="increase"
      >
        Increase size
      </router-link>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from '@vue/runtime-core'
// import axios from 'axios'
export default {
  name: 'EventList',
  props: {
    page: {
      type: Number,
      required: true
    },
    perPage: {
      type: Number,
      required: false
    }
  },
  components: {
    EventCard // register it as a child component
  },
  computed: {
    hasNextPage() {
      let totalPages = Math.ceil(this.totalEvents / 2)
      return this.page < totalPages
    }
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
    watchEffect(() => {
      EventService.getEvents(this.perPage , this.page)
        .then((response) => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch((error) => {
          console.log(error)
        })
    })
  }
}
</script>
<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
.pageLimit {
  display: flex;
  width: 400px;
  margin-top: 15px;
}
.pageLimit a {
  flex: 1;
  text-decoration: none;
  color: limegreen;
}
#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}
</style>
