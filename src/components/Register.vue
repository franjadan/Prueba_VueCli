<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-9 col-md-7 col-lg-5 mx-auto">
        <div class="card card-signin my-5">
          <div class="card-body">
            <h5 class="card-title text-center">Register</h5>
            <form class="index.html">
              <div class="form-label-group p-2">
              <input type="email" class="form-control" id="email" v-model="email" placeholder="Email">
              </div>
              <div class="form-label-group p-2">
              <input type="password" class="form-control" id="password" v-model="password" placeholder="Password">
              </div>
              <button @click="register" class="btn btn-lg btn-primary btn-block text-uppercase mt-3">Register</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import firebase from 'firebase';

export default {
    name: 'register',
    data: function(){
        return {
            email: '',
            password: ''
        };
    },
    methods: {
        register: function(e){
            firebase
            .auth()
            .createUserWithEmailAndPassword(this.email, this.password)
            .then(user => {
                alert("Cuenta creada para " + + user.user.email);
                this.$router.go({path: this.$router.path});
            },
            err => {
                alert(err.message);
            });
            e.preventDefault();
    
        }
    }
};
</script>