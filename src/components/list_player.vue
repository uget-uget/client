<template>
<div>
    <div class="card bg-success" style="width : 18rem">
        <div class="card-header bg-warning"> <h2 class="text-black font-weight-bold"> Rating Player : </h2> </div>
        <div class="card-body p-3">
            <ul class="list-group">
               <li v-for="(player, index) in players" :key="index" class="list-group-item bg-dark border border-white mb-1">
                   <div class="row">
                       <div class="col-6">
                          <img class="rounded-circle" src="../assets/larva.jpg" alt="doesn't get" width="50px" height="50px">
                       </div>
                       <div class="col-6">
                           <div class="row">
                               <h6 class="text-capitalize text-white font-weight-bold"> {{ player.name}}</h6> 
                           </div>
                           <div class="row">
                               <span class="text-danger font-weight-bold"> Score :</span> <span class="text-warning"> {{ player.score}}</span>
                           </div>
                       </div>
                   </div>
                </li> 
            </ul>
        </div>
    </div>
</div>
</template>
<script>
import firebase,{ functions } from 'firebase'
import database from '../assets/config'


export default {
    props : ['play'],
    data (){
        return {
            database : null,
            players : [],
        }
    },
    methods : {
        getPlayer : function(){
            let user = localStorage.getItem('user') ? localStorage.getItem('user') : null
            this.self = user
        }
    },
    mounted(){
        //localStorage.setItem('room', 'room1')
       
        let roomName = localStorage.getItem('room')
        const roomRef = database.ref(`room/${roomName}`)

        roomRef.on('value',snapshot => {
           
            let data = Object.values(snapshot.val().users)
            this.players = data

            this.players = data.sort( function(a, b){
                if (a.score < b.score) return 1;
                if (a.score > b.score) return -1;
                return 0;
            })
           
        })
        
    }
}
</script>
