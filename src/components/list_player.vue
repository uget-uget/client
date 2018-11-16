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


export default {
    props : ['play'],
    data (){
        return {
            database : null,
            avatars :  ['player1.png', 'player2.png', 'player3.png'],
            players : [],
        }
    },
    methods : {
        cekStatus : function(data){
            let player_winner = null
            let player_loser = null

            for( let i in data){
                if( data[i].status !== 'life'){
                    player_loser = Number(i)
                    if ( i == '0' ){
                        player_winner = 1
                    }else{
                        player_winner = 0
                    }
                    return { player_winner,player_loser }
                }
            }
            
            return false

        },
        getPlayer : function(){
            let user = localStorage.getItem('user') ? localStorage.getItem('user') : null
            this.self = user
        }
    },
    mounted(){
        this.getPlayer()
        var config = {
            apiKey: "AIzaSyBa8sAhAyZppmkwFsE-9cB0nno0WcvurXc",
            authDomain: "list-player-41db2.firebaseapp.com",
            databaseURL: "https://list-player-41db2.firebaseio.com",
            projectId: "list-player-41db2",
            storageBucket: "list-player-41db2.appspot.com",
            messagingSenderId: "815573183322"
        };

        firebase.initializeApp(config);
        
        
        const database = firebase.database()
        this.database = database
       
        const roomRef = database.ref('room/')

        roomRef.on('value',snapshot => {


            let data = Object.values(snapshot.val())

            let end_play = this.cekStatus(data)
            if ( end_play ) {
                let newData = []
               newData[0] = data[end_play.player_winner]
               newData[1] = data[end_play.player_loser]
               this.players = newData
            }else {
                this.players = data.sort( function(a, b){
                    if (a.score < b.score) return 1;
                    if (a.score > b.score) return -1;
                    return 0;
                })
            }
        })
        
    }
}
</script>
