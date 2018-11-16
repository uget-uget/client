<template>
    <div>
        <img src="https://i.gifer.com/Z5cT.gif" alt="uget uget muter muter">
        <h3>Be patient... waiting for oponent</h3>
    </div>
</template>


<script>
import database from '../assets/config'

export default {
    data() {
        return {
            room: '',
            user_id: ''
        }
    },
    methods: {
        checkUser () {
            let roomvalue = localStorage.getItem('room')
            let user_id = localStorage.getItem('user_id')
            this.room = roomvalue
            this.user_id = user_id
            let hangroom = database.ref('room/' + this.room)
            let self = this
            hangroom.on('value', function (snapshot) {
                let userarr = snapshot.val().users
                console.log('ini userArr', userarr);
                
                if (Object.keys(userarr).length === 2) { //full-room
                    self.$router.push('home')
                }
            })
        },
    },
    mounted () {
    this.checkUser()
  }
}
</script>
