<template>
    <div>
        <h1>Welcome to Uget-Uget!</h1>
        <div>
            <br>
            <br>
            <!-- <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Quae laborum voluptatibus deleniti possimus inventore similique, itaque unde dolorum illum voluptatem mollitia in voluptates, doloribus a veniam, ea accusantium. Dolor, ex.</p> -->
            <div>
                <button class="btn btn-info" data-toggle="modal" data-target="#createRoomModal">Create New Game</button>
                <br>
<!--                 
                <label>Input Player Owner's Name: </label>
                <input v-model="player_2" type="text" placeholder="player name">
                <button v-on:click="addPlayer2()">Tambah player 2</button> -->

                <div style="margin-top: 40px;">
                    <div v-on:click="setRoom(room)" v-for="(room, index) in room_list" v-bind:key="index" style="margin-top: 5px;"><button  class="btn btn-light" style="width: 40%;" data-toggle="modal" data-target="#joinRoomModal">{{ room }}</button><br></div>
                    
                </div>
            </div>
        </div>

        <div class="modal fade" id="createRoomModal" tabindex="-1" role="dialog" aria-labelledby="createRoomModalLabel" aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="createRoomModalLabel">Create New Game</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <label>Room Name: </label>
                        <input v-model="room" class="form-control"  type="text" placeholder="room name">
                        <br>
                        <label>Your Name: </label>
                        <input v-model="player_1" class="form-control" type="text" placeholder="player name">
                        <br>
                        <label>Difficulty: </label>
                        <select class="form-control" name="" id="" v-model='difficulty'>
                            <option value="1000" selected>Baby</option>
                            <option value="700">Child</option>
                            <option value="500">Teen</option>
                            <option value="300">Adult</option>
                            <option value="100">Professional</option>
                            <option value="30">God of Snake Game</option>
                        </select>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button v-on:click="createRoom()" type="button" class="btn btn-primary" data-dismiss="modal">Create Room</button>
                    </div>
                </div>
            </div>
        </div>

         <div class="modal fade" id="joinRoomModal" tabindex="-1" role="dialog" aria-labelledby="joinRoomModalLabel" aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="joinRoomModalLabel">Join Room {{ room }}</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <label>Your Name: </label>
                        <input v-model="player_2" class="form-control" type="text" placeholder="player name">
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button v-on:click="addPlayer2()" type="button" class="btn btn-primary" data-dismiss="modal">Join</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>



<script>

import database from '../assets/config'

export default {
    name: 'HomePage',
    data() {
        return {
            room: '',
            difficulty: '',
			room_list: [],
            player_1: '',
            player_2: '',
        }
    },
    created () {
        this.getAllRoom()
    },

    methods: {
        createRoom() {
			console.log(this.room);
			let self = this

			if(self.room) {
				let hangroom = database.ref('room/' + self.room)
				hangroom.once('value', function (snapshot) {
					let result = snapshot.val()
					console.log('check room', result)
					if (!result) {
						database
							.ref('room/' + self.room)
							.set({
                                name: self.room,
                                difficulty: self.difficulty,
                                available: true,
								users: []
							})
							.then(response => {

                                database.ref('room/' + self.room + '/users').push({
                                    name: self.player_1, 
                                    score: 0,
                                    status: 'life'
                                })
                                .then(response => {
                                    // console.log(response.key)
                                    localStorage.setItem('room', self.room)
                                    localStorage.setItem('user_id', response.key)
                                    self.$router.push('loading')
                                })
								
							})
					} else {
						let roomusers = result.users
						if (roomusers && Object.keys(roomusers).length >= 2) {
							localStorage.removeItem('room')
							alert('Full Room. Please choose another!')
							window.location.reload()
						} 
						else {
							localStorage.setItem('room', self.room)
							self.$router.push('loading')
						}
					}
				})
			}
			
        },
        addPlayer2() {
            let self = this
            database.ref('room/' + self.room + '/users').push({
                name: self.player_2, 
                score: 0,
                status: 'life'
            })
            .then(response => {
                console.log('whoami', response.key)
                localStorage.setItem('room', self.room)
                localStorage.setItem('user_id', response.key)
                self.$router.push('home')
            })
            

        },
        getAllRoom() {
            let self = this
            database.ref('room/').on('value', function (snapshot) {
                console.log('get all room', snapshot.val())
                let result = snapshot.val()

                let data = Object.keys(result)

                // for (let i = 0; i < data.length; i++) {

                // }
                self.room_list = Object.keys(result)

                console.log('ini semua', self.room_list);
                
            })
        },
        setRoom(room) {
            this.room = room
        },
    }
}
</script>


<style scoped>
p {
    color: white;
}
</style>

