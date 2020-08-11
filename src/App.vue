<template>
  <div id="app">
    <div id="nav">
      <Navigation :user="user" @logout="logout" />
    </div>
    <router-view
      class="container"
      :user="user"
      :meetings="meetings"
      :error="error"
      @logout="logout"
      @addMeeting="addMeeting"
      @deleteMeeting="deleteMeeting"
      @checkIn="checkIn"
    />
  </div>
</template>

<script>
import db from "./db.js";
import Firebase from "firebase";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import Navigation from "@/components/Navigation.vue";

export default {
  name: "app",
  data: function() {
    return {
      user: null,
      meetings: [],
      error: null
    };
  },
  methods: {
    logout: function() {
      Firebase.auth()
        .signOut()
        .then(() => {
          this.user = null;
          this.$router.push("/login");
        });
    },
    addMeeting: function(payload) {
      db.collection("users")
        .doc(this.user.uid)
        .collection("meetings")
        .add({
          name: payload,
          createdAt: Firebase.firestore.FieldValue.serverTimestamp()
        });
    },
    deleteMeeting: function(payload) {
      db.collection("users")
        .doc(this.user.uid)
        .collection("meetings")
        .doc(payload)
        .delete();
    },
    checkIn: function(payload) {
      db.collection("users")
        .doc(payload.userId)
        .collection("meetings")
        .doc(payload.meetingId)
        .get()
        .then(doc => {
          if (doc.exists) {
            console.log(
              "App.vue CheckIn Doc exists. Payload email is; " +
                payload.eMail +
                "."
            );
            db.collection("users")
              .doc(payload.userId)
              .collection("meetings")
              .doc(payload.meetingId)
              .collection("attendees")
              .add({
                displayName: payload.displayName,
                eMail: payload.eMail,
                createdAt: Firebase.firestore.FieldValue.serverTimestamp()
              })
              .then(
                () =>
                  console.log(
                    "Should have added payload to attendees collection."
                  ),
                console.log("Payload is:" + payload),
                console.log("Re-routing to home route."),
                this.$router.push("/")
              );
          } else {
            console.log("App.vue checkIn, no such doc exists.");
            this.error = "Sorry, no such meeting";
          }
        });
    }
  },
  mounted() {
    Firebase.auth().onAuthStateChanged(user => {
      if (user) {
        this.user = user;
        db.collection("users")
          .doc(this.user.uid)
          .collection("meetings")
          .orderBy("name")
          .limit(10)
          .onSnapshot(snapshot => {
            const snapData = [];
            snapshot.forEach(doc => {
              snapData.push({
                id: doc.id,
                name: doc.data().name
              });
            });
            this.meetings = snapData.sort((a, b) => {
              if (a.name.toLowerCase() < b.name.toLowerCase()) {
                return -1;
              } else {
                return 1;
              }
            });
          });
      }
    });

    {
      FontAwesomeIcon;
    }
  },
  components: {
    Navigation
  }
};
</script>
<style lang="scss">
$primary: indianred;
$danger: rebeccapurple;
@import "node_modules/bootstrap/scss/bootstrap";
</style>
