<template>
  <div id="home">
    <h1 class="mt-3">Control de asistencias</h1>

        <download-excel v-if="admin" class="btn btn-primary pull-right" :data = "assists" :fields = "json_fields" worksheet ="My Worksheet" name ="filename.xls">
            Download Excel
        </download-excel>

        <grid :data="assists" :columns="columns" :keys="keys"></grid>
  </div>
</template>

<script>
import db from './firebaseInit';
import firebase from 'firebase';
import Vue from 'vue'
import JsonExcel from 'vue-json-excel';
import Grid from './Grid'
Vue.component('downloadExcel', JsonExcel);

export default {
    name: 'home',
    data: function(){
        return{
            assists: [],
            columns: ['Empleado', 'Entrada', 'Salida'],
            keys: ['user', 'in', 'out'],
            admin: false,
            json_fields: {
                'Empleado': 'user',
                'Entrada': 'in',
                'Salida': 'out'
            },
            json_meta: [
                [
                    {
                        'key': 'charset',
                        'value': 'utf-8'
                    }
                ]
            ]
        }
    },
    components: {
        Grid
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
                    this.admin = true;
                    
                    firebase
                    .firestore()
                    .collection("assists")
                    .get()
                    .then(querySnapshot => {
                        querySnapshot.forEach(doc => {
                            
                            let dateIn = new Date(doc.data().In.seconds * 1000);
                            let dateOut = new Date(doc.data().Out.seconds * 1000);

                            const data = {
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