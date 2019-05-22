<template>
  <div id="home">
    <h1 class="mt-3">Control de asistencias</h1>
		<ul class="list-group">
			<li v-for="assistance in assists" v-bind:key="assistance.id" class="list-group-item">
				<div>
					{{ assistance.user }}, {{ assistance.in }}, {{ assistance.out }}
				</div>
			</li>
		</ul>
  </div>
</template>

<script>
import db from './firebaseInit';
import firebase from 'firebase';

export default {
    name: 'home',
    data: function(){
        return{
            assists: []
        };
    },

    created(){

        firebase
        .firestore()
        .collection("users")
        .where("email", "==", firebase.auth().currentUser.email)
        .get()
        .then(querySnapshot => {
            if(querySnapshot.size > 0){
                if(querySnapshot.docs[0].data().rol == "administrador"){
                    console.log("Admin");

                    firebase
                    .firestore()
                    .collection("assists")
                    .get()
                    .then(querySnapshot => {
                        querySnapshot.forEach(doc => {
                            
                            let dateIn = new Date(doc.data().In.seconds * 1000);
                            let dateOut = new Date(doc.data().Out.seconds * 1000);

                            const data = {
                                id: doc.data().id,
                                user: doc.data().user,
                                in: dateIn.getDay() + "/" + (dateIn.getMonth() + 1) + "/" + dateIn.getFullYear() + " " + dateIn.getHours() + ":" + dateIn.getMinutes() + ":" + dateIn.getSeconds(),
                                out: dateOut.getDay() + "/" + (dateOut.getMonth() + 1) + "/" + dateOut.getFullYear() + " " + dateOut.getHours() + ":" + dateOut.getMinutes() + ":" + dateOut.getSeconds(),
                            };
                            
                            this.assists.push(data);
                        });
                    })
                }else{
                    console.log("No Admin");

                    firebase
                    .firestore()
                    .collection("assists")
                    .where("user", "==", firebase.auth().currentUser.email)
                    .get()
                    .then(querySnapshot => {
                        querySnapshot.forEach(doc => {
                            
                            let dateIn = new Date(doc.data().In.seconds * 1000);
                            let dateOut = new Date(doc.data().Out.seconds * 1000);

                            const data = {
                                id: doc.data().id,
                                user: doc.data().user,
                                in: dateIn.getDay() + "/" + (dateIn.getMonth() + 1) + "/" + dateIn.getFullYear() + " " + dateIn.getHours() + ":" + dateIn.getMinutes() + ":" + dateIn.getSeconds(),
                                out: dateOut.getDay() + "/" + (dateOut.getMonth() + 1) + "/" + dateOut.getFullYear() + " " + dateOut.getHours() + ":" + dateOut.getMinutes() + ":" + dateOut.getSeconds(),
                            };
                            
                            this.assists.push(data);
                        });
                    })
                }
            }
        },
        err => {
            alert(err.message);
        });
    }
};

</script>