<template>
  <form class="mt-3" @submit.prevent="handleCheckIn">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-6">
          <div class="card bg-light">
            <div class="card-body">
              <h3 class="font-weight-light mb-3">Check In</h3>
              <!-- <h4>{{ meetingDeets.name }}</h4> -->
              <section class="form-group">
                <div class="col-12 alert alert-danger px-3" v-if="error">
                  {{ error }}
                </div>
                <label class="form-control-label sr-only" for="displayName"
                  >Name</label
                >
                <input
                  required
                  class="form-control"
                  type="text"
                  placeholder="name"
                  v-model="displayName"
                />
              </section>
              <section class="form-group">
                <label class="form-control-label sr-only" for="Email"
                  >Email</label
                >
                <input
                  required
                  class="form-control"
                  type="email"
                  placeholder="Email"
                  v-model="eMail"
                />
              </section>
              <div class="form-group text-right mb-0">
                <button class="btn btn-primary" type="submit">Check In</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </form>
</template>
<script>
// import db from "../db.js";
export default {
  data: function() {
    return {
      displayName: null,
      eMail: null,
      meetingDeets: null
    };
  },
  props: ["error", "meetings"],
  methods: {
    handleCheckIn: function() {
      console.log(
        "CheckIn.vue Calling emit with meeting ID " +
          this.$route.params.meetingId +
          " and user ID " +
          this.$route.params.userId +
          "."
      );
      this.$emit("checkIn", {
        userId: this.$route.params.userId,
        meetingId: this.$route.params.meetingId,
        displayName: this.displayName,
        eMail: this.eMail
      });
      this.displayName = null;
      this.eMail = null;
    }
    //   },
    //   mounted() {
    //       db.collection("users")
    //       .doc(this.userId)
    //       .collection("meetings")
    //       .doc(this.meetingId)
    //       .onSnapshot(snapshot => {
    //           const snapData = [];
    //           snapshot.forEach(doc => {
    //               snapData.push({
    //                   id: doc.id,
    //                   attendees: doc.attendees,
    //                   eMail: doc.eMail,
    //                   name: doc.name
    //               });
    //           });
    //           this.meetingDeets = snapData;
    //         });
  }
};
</script>
