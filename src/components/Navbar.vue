 <template>
    <div class="d-flex flex-column flex-md-row align-items-center p-3 px-md-4 mb-3 bg-white border-bottom shadow-sm">
        <h5 class="my-0 mr-md-auto font-weight-normal">D'rgachi</h5>
        <nav class="my-2 my-md-0 mr-md-3">
            <router-link class="p-2 text-dark" v-if="!isLoggedIn" to="/login">Login</router-link>
            <router-link class="p-2 text-dark" v-if="!isLoggedIn" to="/register">Register</router-link>
        </nav>
        <button v-if="isLoggedIn" v-on:click="logout" class="btn btn-outline-primary">Logout</button>

    </div>
  </template>

  <script>
import firebase from 'firebase';
export default {
  name: 'navbar',
  data() {
    return {
      isLoggedIn: false,
      currentUser: false
    };
  },
  created() {
    if (firebase.auth().currentUser) {
      this.isLoggedIn = true;
      this.currentUser = firebase.auth().currentUser.email;
    }
  },
  methods: {
    logout: function() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.$router.go({ path: this.$router.path });
        });
    }
  }
};
</script>

<style scoped>
.email {
  padding-right: 10px;
}
</style>
  