<template>
  <div class="container mt-4">
    <div class="row justify-content-center">
      <div class="col-md-8" v-if="user !== null && user.uid == userId">
        <h1 class="font-weight-light text-center">Attendees</h1>

        <div class="card bg-light mb-4">
          <div class="card-body text-center">
            <div class="input-group input-group-lg">
              <input
                type="text"
                placeholder="Search Attendees"
                class="form-control"
                v-model="searchQuery"
                ref="searchQuery"
              />
              <div class="input-group-append">
                <button
                  class="btn btn-sm btn-outline-info"
                  title="Pick a random attendee"
                  @click="chooseRandom"
                >
                  <font-awesome-icon icon="random"></font-awesome-icon>
                </button>
                <button
                  class="btn btn-sm btn-outline-info"
                  title="Reset Search"
                  @click="resetQuery"
                >
                  <font-awesome-icon icon="undo"></font-awesome-icon>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row justify-content-center">
      <div
        class="col-8 col-sm-6 col-md-4 col-lg-3 mb-2 p-0 px-1"
        v-for="item in filteredAttendees"
        :key="item.id"
      >
        <div class="card">
          <div
            class="card-body px-3 py-2 d-flex align-items-center justify-content-center"
          >
            <div
              class="btn-group pr-2"
              v-if="user != null && user.uid == userId"
            >
              <button
                class="btn btn-sm"
                :class="[item.star ? 'text-danger' : '']"
                title="Give user a Star"
                @click="toggleStar(item.id)"
              >
                <font-awesome-icon icon="star"></font-awesome-icon>
              </button>
              <a
                class="btn btn-sm btn-outline-seconndary"
                title="Send user an Email"
                :href="'mailto:' + item.eMail"
              >
                <font-awesome-icon icon="envelope"></font-awesome-icon>
              </a>
              <button
                class="btn btn-sm btn-outline-seconndary"
                title="Delete Attendee"
                @click="deleteAttendee(item.id)"
              >
                <font-awesome-icon icon="trash"></font-awesome-icon>
              </button>
            </div>
            <div>{{ item.displayName }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import db from "../db.js";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

export default {
  name: "Attendees",
  data: function() {
    return {
      attendees: [],
      displayAttendees: [],
      userId: this.$route.params.userId,
      meetingId: this.$route.params.meetingId,
      searchQuery: ""
    };
  },
  props: ["user"],
  components: { FontAwesomeIcon },
  computed: {
    filteredAttendees: function() {
      const dataFilter = item =>
        item.displayName.toLowerCase().match(this.searchQuery.toLowerCase()) &&
        true;
      return this.displayAttendees.filter(dataFilter);
    }
  },
  methods: {
    deleteAttendee: function(attendeeId) {
      if (this.user && this.user.uid == this.userId) {
        db.collection("users")
          .doc(this.user.uid)
          .collection("meetings")
          .doc(this.meetingId)
          .collection("attendees")
          .doc(attendeeId)
          .delete();
      }
    },
    toggleStar: function(attendeeId) {
      if (this.user && this.user.uid == this.userId) {
        const ref = db
          .collection("users")
          .doc(this.user.uid)
          .collection("meetings")
          .doc(this.meetingId)
          .collection("attendees")
          .doc(attendeeId);

        ref.get().then(doc => {
          const star = doc.data().star;
          if (star) {
            ref.update({
              star: !star
            });
          } else {
            ref.update({
              star: true
            });
          }
        });
      }
    },
    chooseRandom: function() {
      const randomAttendee = Math.floor(Math.random() * this.attendees.length);
      this.displayAttendees = [this.attendees[randomAttendee]];
    },
    resetQuery() {
      this.displayAttendees = this.attendees;
      this.searchQuery = "";
      this.$refs.searchQuery.focus();
    }
  },
  mounted() {
    db.collection("users")
      .doc(this.userId)
      .collection("meetings")
      .doc(this.meetingId)
      .collection("attendees")
      .onSnapshot(snapshot => {
        const snapData = [];
        snapshot.forEach(doc => {
          snapData.push({
            id: doc.id,
            eMail: doc.data().eMail,
            displayName: doc.data().displayName,
            star: doc.data().star
          });
        });
        this.attendees = snapData;
        this.displayAttendees = this.attendees;
      });
  }
};
</script>
