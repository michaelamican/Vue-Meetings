<template>
  <div class="mt-3">
    <div v-if="user" class="text-center">
      Welcome Back
      <span class="font-weight-bold text-info">{{ user.displayName }}</span
      >,
      <a href="#" class="text-primary" role="button" @click="$emit('logout')"
        >Logout</a
      >
      <div class="container text-center">
        <div class="col-10 col-md-10 col-lg-8 col-xl-7">
          <h4 class="display-4 text-primary mb-2">Meeting Log</h4>
          <p class="lead">
            This simple app creates meetings, allows people to check in, and
            picks random users to award giveaways. It's a good example of a
            Single Page Application which includes connection to a database and
            routing. It's a practical way to learn
            <a target="blank" href="https://vuejs.org/">Vue.js</a> with
            <a target="blank" href="https://firebase.google.com">Firebase</a>.
            <font-awesome-icon
              class="text-danger"
              icon="star"
            ></font-awesome-icon>
          </p>
          <router-link
            class="btn btn-outline-primary mr-2"
            to="/register"
            v-if="!user"
            >Register</router-link
          >
          <router-link
            class="btn btn-outline-primary mr-2"
            to="/login"
            v-if="!user"
            >Login</router-link
          >
          <router-link class="btn btn-primary" to="/meetings" v-if="user"
            >Meetings</router-link
          >
        </div>
      </div>
    </div>
    <div v-else>
      <div class="container text-center">
        <div class="col-10 col-md-10 col-lg-8 col-xl-7">
          <h4 class="display-4 text-primary mb-2">Meeting Log</h4>
          <p class="lead">
            This simple app creates meetings, allows people to check in, and
            picks random users to award giveaways. It's a good example of a
            Single Page Application which includes connection to a database and
            routing. It's a practical way to learn
            <a target="blank" href="https://vuejs.org/">Vue.js</a> with
            <a target="blank" href="https://firebase.google.com">Firebase</a>.
            <font-awesome-icon
              class="text-danger"
              icon="star"
            ></font-awesome-icon>
          </p>
          <p class="font-weight-bold">
            You are not logged in. Please log in to continue, or register below!
          </p>
          <router-link class="btn btn-outline-primary mr-2" to="/register"
            >Register</router-link
          >
          <router-link class="btn btn-outline-primary mr-2" to="/login"
            >Login</router-link
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  props: ["user"]
};
</script>
