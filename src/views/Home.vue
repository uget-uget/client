<template>
  	<div>
			<div v-if="!gameOver">
				<p>Score: <b>{{ score }}</b></p>
				<div v-if="game_on" class="game">
					<span v-for="x in list" :key='list.indexOf(x)' :data-id="x" class="px">
						<span v-for="y in list" :key='list.indexOf(y)' :data-id="y" class="px col" 
							:class="{
								test: testCoord(x,y),
								head: isHead(x,y), 
								snake: isSnake(x,y), 
								food: isFood(x,y), 
								poison: isPoison(x,y), 
								booster: isBooster(x,y), 
								slower: isSlower(x,y), 
								bomb1: isBomb(x,y), 
								bomb2: isBomb2(x,y), 
								bomb3: isBomb3(x,y), 
								bomb4: isBomb4(x,y)}">
								</span>
					</span>
				</div>
				<div>
					<h4>Select Difficulty:</h4>
					<select name="" id="" v-model='difficulty'>
						<option value="300">Baby</option>
						<option value="100">Professional</option>
						<option value="30">God of Snake Game</option>
							<option value="0">More than Imposible</option>
					</select>
					<button @click='changeDifficulty'>Change</button>
				</div>
			</div>
			<!-- Ending -->
			<div v-else>
				<h1>You Lose!! {{ msg }}</h1>
				<button @click="restart">New Game</button>
			</div>
			
		</div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'home',
  components: {
    HelloWorld
  },
  data: function(){
		return {
			total : 12,
			head: {},
			snake: [],
			snakeLenth: 1,
			snakeDirection: 'right',
			food: {},
			poison: {},
			booster: {},
			slower: {},
			bomb: [],
      game_on: true,
      difficulty: 300,
			interval: null,
			intervalBooster: null,
			gameOver: false,
			msg: ''
		}
	},

	created: function(){
		this.init();

	},
	destroyed: function(){
		window.removeEventListener('keydown', this.changeDirection);
	},
	computed: {
		list: function(){
			var x = [];

			for (var i = 0 ; i <= this.total ; i++) {
				x.push(i);
			}

			return x;
		},
		score: function(){
			return this.snakeLenth-1;
		}
	},
	watch: {
		
	},
	methods: {
		playAudio (link) {
			let audio = new Audio()
			audio.src = link
			audio.play()
		},
		init(){
			this.newGame();
			window.addEventListener('keydown', this.changeDirection);
			this.interval = setInterval(this.move, this.difficulty);
		},
		newGame(){
			var self = this;

			this.snakeLenth = 1;
			self.snake = [];
			self.bomb = [];
			
			let snakeCoord = self.getRand()
			self.snake.push(snakeCoord);
			self.head = snakeCoord
			self.snake.push({x:snakeCoord.x++, y:snakeCoord.y++})
			
			self.poison = self.getRand();
			let bombCoord = self.getRand()
			self.bomb.push(bombCoord)
			self.bomb.push({x:(bombCoord.x)+1, y:(bombCoord.y)+1})
			self.bomb.push({x:(bombCoord.x)+1, y:bombCoord.y})
			self.bomb.push({x:bombCoord.x, y:(bombCoord.y)+1})
			self.booster = self.getRand()
			self.slower = self.getRand()
			self.food = self.getRand();
			while((self.food.x == self.poison.x && self.food.y == self.poison.y) || 
						(self.food.x == (self.bomb[0].x) && self.food.y == (self.bomb[0].y)) || 
						(self.food.x == (self.bomb[1].x) && self.food.y == (self.bomb[1].y)) ||
						(self.food.x == (self.bomb[2].x) && self.food.y == (self.bomb[2].y)) ||
						(self.food.x == (self.bomb[3].x) && self.food.y == (self.bomb[3].y)) ) {
						// this.newGame()
						self.food = self.getRand()
			}

		},
		restart() {
			
			this.gameOver = false
			this.newGame()
		},
		move(){
			
			var self = this;
			var last = self.snake[self.snake.length-1];

			var x = last.x;
			var y = last.y;


			if(x == this.food.x && y == this.food.y){
				this.eat();
			}
			if(x == this.poison.x && y == this.poison.y) {
				this.poisoned()
			}
			if(x == this.booster.x && y == this.booster.y) {
				this.boosting()
			}
			if(x == this.slower.x && y == this.slower.y) {
				this.slowing()
			}
			if((x == this.bomb[0].x && y == this.bomb[0].y) ||
				 (x == (this.bomb[1].x) && y == (this.bomb[1].y)) ||
				 (x == (this.bomb[2].x) && y == (this.bomb[2].y)) ||
				 (x == this.bomb[3].x && y == (this.bomb[3].y))) {

				this.playAudio('Bomb-SoundBible.com-891110113.mp3')
				self.msg = 'Game Over!, you eat a bomb stupid...'
				self.gameOver = true
				clearInterval(this.interval)
				this.$emit('gameOver', this.score)
				this.$router.replace('/ending')
			}


			switch( self.snakeDirection ){
				case 'up':
					y -= 1;
				break;

				case 'right':
					x += 1;

				break;

				case 'down':
					y += 1;

				break;

				case 'left':
					x -= 1;
				break;
			}


			if(y > self.total){
				y = 0;
			}

			if(x > self.total){
				x = 0;
			}

			if(y < 0){
				y = self.total;
			}

			if(x < 0){
				x = self.total;
			}

				// self bite
			for(let i=1; i < this.snake.length; i++){
				if(this.snake[i] != undefined && this.snake[i].x == x && this.snake[i].y == y){
					self.msg = 'Game over. due to you eat yourself,  Your score: ' + this.score
					self.gameOver = true;
					clearInterval(this.interval)
					this.$emit('gameOver', this.score)
					this.$router.replace('/ending')
				}
			}

			self.snake.push({x: x, y: y})

			if(self.snake.length > self.snakeLenth){
				self.snake.shift();
			}
		},
		isHead(x,y){
			if(this.snake[this.snake.length-1].x == x && this.snake[this.snake.length-1].y==y){
				return true;
			}
		},
		isSnake(x,y){
			for(let i in this.snake){
				if(this.snake[i].x == x && this.snake[i].y == y && i != this.snake.length-1){
					// isHead(x,y)
					return true;
				}
			}
		},
		testCoord(x,y){
			if(x == 5 && y == 11) {
				return true;
			}
		},
		isFood(x,y){
			if((this.food.x == x && this.food.y == y)){
				return true;
			}
		},
		isPoison(x,y){
				if(this.poison.x == x && this.poison.y == y){
				return true;
			}
		},
		isBomb(x,y){
				if((this.bomb[0].x == x && this.bomb[0].y == y)){
				return true;
			}
		},
		isBomb2(x,y){
				if((this.bomb[1].x == x && this.bomb[1].y == y)){
				return true;
			}
		},
		isBomb3(x,y){
				if((this.bomb[2].x == x && this.bomb[2].y == y)){
				return true;
			}
		},
		isBomb4(x,y){
				if((this.bomb[3].x == x && this.bomb[3].y == y)){
				return true;
			}
		},
		isBooster(x,y) {
			if((this.booster.x == x && this.booster.y == y)){
				return true;
			}
		},
		isSlower(x,y) {
			if((this.slower.x == x && this.slower.y == y)){
				return true;
			}
		},
		eat(){
			this.playAudio('Bite-SoundBible.com-2056759375.mp3')
			this.snakeLenth += 1;
			this.food = this.getRand();
			this.poison = this.getRand();
			// let bombCoord = this.getRand()
			// this.bomb = [
			// 	bombCoord,
			// 	{x:(bombCoord.x)+1, y:(bombCoord.y)+1},
			// 	{x:(bombCoord.x)+1, y:(bombCoord.y)},
			// 	{x:(bombCoord.x), y:(bombCoord.y)+1}
			// ]
				while((self.food.x == self.poison.x && self.food.y == self.poison.y) || 
						(self.food.x == (self.bomb[0].x) && self.food.y == (self.bomb[0].y)) || 
						(self.food.x == (self.bomb[1].x) && self.food.y == (self.bomb[1].y)) ||
						(self.food.x == (self.bomb[2].x) && self.food.y == (self.bomb[2].y)) ||
						(self.food.x == (self.bomb[3].x) && self.food.y == (self.bomb[3].y)) ) {
						self.food = self.getRand()
			}
		},
		poisoned() {
			this.playAudio('Toxic Goo-SoundBible.com-392739082.mp3')
			this.snakeLenth--;
			this.poison = this.getRand();
		},
		boosting() {
			this.playAudio('Electric Shock Zap-SoundBible.com-68983399.mp3')
			clearInterval(this.interval)
			this.interval = setInterval(this.move, (this.difficulty * 1/2) );
			setTimeout(()=>{
				this.changeDifficulty()
				this.booster = this.getRand()
			}, 4000)
		},
		slowing() {
			this.playAudio('Slug Crawling-SoundBible.com-2052984959.mp3')
			clearInterval(this.interval)
			this.interval = setInterval(this.move, (this.difficulty * 2) );
			setTimeout(()=>{
				this.changeDifficulty()
				this.slower = this.getRand()
			}, 3000)
		},
		changeDirection(e){

			e.preventDefault();
			e.stopPropagation();

			var directions = {
				37 : 'left',
				38 : 'up',
				39 : 'right',
				40 : 'down',
			}

			if(directions[e.keyCode] !== undefined){

				if( (this.snakeDirection == 'right' && directions[e.keyCode] == 'left') ||
					(this.snakeDirection == 'left' && directions[e.keyCode] == 'right') ||
					(this.snakeDirection == 'down' && directions[e.keyCode] == 'up') ||
					(this.snakeDirection == 'up' && directions[e.keyCode] == 'down') ) {

					return false;
				}

				this.snakeDirection = directions[e.keyCode];
			}

			return false;
		},

		getRand(){
			return {x: Math.floor(Math.random()*this.total), y: Math.floor(Math.random()*this.total)}
    },
    changeDifficulty() {
      clearInterval(this.interval)
      this.interval = setInterval(this.move, this.difficulty);
    }
  },
}
</script>

<style scoped>
  h1, h4, p {
		background: teal;
    color: whitesmoke;
  }
</style>

