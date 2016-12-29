<template>
	<div id="app">

		<div class="title" v-show="questions[question] && questions[question].title">
			<h1 v-if="questions[question] && questions[question].title">{{questions[question].title}}</h1>
		</div>

		<div class="app-section questions" >
			<transition name="move" v-for="(q, index) in questions">
				<question v-show="index == question" :img="q.img" :header="q.header" :subheader="q.subheader"></question>
			</transition>

			<transition name="drop">

				<question 	v-show="question >= questions.length && winners.length === 1"
							img="questions/winner.jpg"
							:header="winners[0].name"
							subheader="GEFELICITEERD!">

				</question>

			</transition>
			<transition name="drop">
				<question 	v-show="question >= questions.length && winners.length > 1"
							img="questions/final-question.jpg"
							header="Benaderingsvraag!">

				</question>
			</transition>
		</div>
		<div class="app-section scores">
			<team v-for="team in teamResults" :team="team"></team>
		</div>

	</div>
</template>

<script>
import Team from './components/Team.vue'
import Question from './components/Question.vue'


export default {
	components: {
		Team,
		Question
	},
	name: 'app',
	computed: {
		teamResults() {
			var max = 0;
			this.teams.forEach((team) => {
				if (team.score > max) {
					max = team.score;
				}
			});

			this.teams.forEach((team) => {
				this.$set(team, 'leader',(team.score === max));
				this.$set(team, 'active',(team === this.activeTeam));
				this.$set(team, 'scale', (max > 0) ? (team.score / max * 0.25) + 0.75 : 1);
			});

			return this.teams;
		},
		winners() {
			return this.teamResults.filter((team) => {
				return team.leader;
			})
		},
		answeredCorrect() {
			return this.teams.filter((team) => {
				return team.answered === true;
			}).length > 0;
		}
	},
	data () {
		return {
			question: 0,
			points: {
				correct: 2,
				incorrect: -1
			},
			activeTeam: false,
			teams: [
				{
					shortcut: 'A',
					name: 'Team A',
					score: 0,
					answered: null

				},
				{
					shortcut: 'B',
					name: 'Team B',
					score: 0,
					answered: null
				},
				{
					shortcut: 'C',
					name: 'Team C',
					score: 0,
					answered: null
				},
				{
					shortcut: 'D',
					name: 'Team D',
					score: 0,
					answered: null
				}
			],
			questions: [
				{img: 'questions/quiz.jpg'},
				{img: 'questions/01.jpg', title: 'Vraag 1, Januari 2016'},
				{img: 'questions/02.jpg', title: 'Vraag 2, Januari 2016'},
				{img: 'questions/03.jpg', title: 'Vraag 3, Februari 2016'},
				{img: 'questions/04.jpg', title: 'Vraag 4, Februari 2016'},
				{img: 'questions/05.jpg', title: 'Vraag 5, Maart 2016'},
				{img: 'questions/06.jpg', title: 'Vraag 6, Maart 2016'},
				{img: 'questions/07.jpg', title: 'Vraag 7, April 2016'},
				{img: 'questions/08.jpg', title: 'Vraag 8, April 2016'},
				{img: 'questions/09.jpg', title: 'Vraag 9, Mei 2016'},
				{img: 'questions/10.jpg', title: 'Vraag 10, Mei 2016'},
				{img: 'questions/11.jpg', title: 'Vraag 11, Juni 2016'},
				{img: 'questions/12.jpg', title: 'Vraag 12, Juni 2016'},
				{img: 'questions/13.jpg', title: 'Vraag 13, Juli 2016'},
				{img: 'questions/14.jpg', title: 'Vraag 14, Juli 2016'},
				{img: 'questions/15.jpg', title: 'Vraag 15, Augustus 2016'},
				{img: 'questions/16.jpg', title: 'Vraag 16, Augustus 2016'},
				{img: 'questions/17.jpg', title: 'Vraag 17, September 2016'},
				{img: 'questions/18.jpg', title: 'Vraag 18, September 2016'},
				{img: 'questions/19.jpg', title: 'Vraag 19, Oktober 2016'},
				{img: 'questions/20.jpg', title: 'Vraag 20, Oktober 2016'},
				{img: 'questions/21.jpg', title: 'Vraag 21, November 2016'},
				{img: 'questions/22.jpg', title: 'Vraag 22, November 2016'},
				{img: 'questions/23.jpg', title: 'Vraag 23, December 2016'},
				{img: 'questions/24.jpg', title: 'Vraag 24, December 2016'},
				{img: 'questions/25.jpg', title: 'Logo\'s!'},
			]
		}
	},
	methods: {
		answered(team, correct) {
			if (!team) {
				return;
			}
			if (correct) {
				this.$set(team, 'score', team.score + this.points.correct);
				this.playSound('correct.mp3');
			} else {
				this.$set(team, 'score', team.score + this.points.incorrect);
				this.playSound('incorrect.mp3');
			}

			this.$set(team, 'answered', correct);

			this.activeTeam = false;
		},
		changeQuestion(next) {
			if (next) {
				if (this.question >= this.questions.length) {
					return;
				}
				this.question++
			} else {
				if (this.question == 0) {
					return;
				}
				this.question--;
			}

			this.playSound('next-question.mp3');
			this.runStateEffects();

			this.activeTeam = false;
			this.teams.forEach((team) => {
				team.answered = null;
			});
		},
		runStateEffects() {
			if (this.question >= this.questions.length && this.winners.length == 1) {
				this.playSound('win.mp3');
			} else if (this.question >= this.questions.length && this.winners.length > 1) {
				this.playSound('final-question.wav');
			}
		},
		playSound(sound) {
			var audio = new Audio('/sounds/' + sound);
			audio.play();
		},
		keyPressed(e) {
			var key = String.fromCharCode(e.keyCode);

			// Select teams
			this.teams.find((team) => {
				if (team.shortcut.toLowerCase() == key.toLowerCase() && !this.activeTeam && team.answered === null && !this.answeredCorrect) {
					this.playSound('answer.mp3');
					this.activeTeam = team;
					return true;
				}
			});

			// Answer Given
			if (key.toLowerCase() === 'g') {
				this.answered(this.activeTeam, true);
			}

			if (key.toLowerCase() === 'f') {
				this.answered(this.activeTeam, false);
			}

			// Next or Previous Question
			if (key === '.' || key === '>') {
				this.changeQuestion(true);
			}

			if (key === ',' || key === '<') {
				this.changeQuestion(false);
			}

			// Reset selected Team
			if (key.toLowerCase() === 'x') {
				this.activeTeam = false;
			}

			// Manually adjust score
			if (key === '-' || key === '_') {
				if (this.activeTeam) {
					this.$set(this.activeTeam, 'score', this.activeTeam.score - 1);
				}
			}

			if (key === '+' || key === '=') {
				if (this.activeTeam) {
					this.$set(this.activeTeam, 'score', this.activeTeam.score + 1);
				}
			}


		}
	},
	watch: {
		teams: {
			handler() {
				this.runStateEffects();
			}, deep: true
		}
	},
	mounted() {
		window.onkeypress = this.keyPressed;
	}
}
</script>

<style lang="sass">
	* {
		padding: 0;
		margin: 0;
	}

	html,
	body {
		height: 100%;
		background-color: black;
		color: white;
		background-image: url(assets/bg.jpg);
		background-size: cover;
		overflow: hidden;
	}

	#app {
		height: 100%;
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		display: flex;
		flex-direction: column;
	}

	.title {
		z-index: 1000;
		position: absolute;
		text-align: center;
		margin-left: 50%;
		transform: translateX(-50%);
		padding: 10px 30px;
		border: 2px solid white;
		border-radius: 5px;
		margin-top: 10px;
		background-color: Black;
		box-shadow: 0px 0px 10px 5px rgba(0, 0, 0, 0.75);
		.question-number {
			font-size: 1.5em;
			font-weight: bold;
			color: #999;
		}
	}

	.questions {
		flex: 1;
		position: relative;
		margin: 20px;
		margin-bottom: 10px;
		.question {
			position: absolute;
			bottom: 0;
			right: 0;
			top: 0;
			left: 0;
			border: 2px solid white;
			border-radius: 5px;
			background-size: cover;
			background-position: center center;
			background-color: black;
			box-shadow: 0px 0px 10px 5px rgba(0, 0, 0, 0.75);
		}
		.final-question {
			h1 {
				font-size: 12vh;
			}
		}

		@keyframes text-animation {
			50% {
				transform: scale(1.2,1.2);
			}
		}
	}

	.scores {
		display: flex;
		flex-direction: row;
		padding: 10px;
		.score {
			box-shadow: 0px 0px 10px 5px rgba(0, 0, 0, 0.75);
			margin: 10px;
			margin-top: 0;
			border: 2px solid white;
			border-radius: 5px;
			flex: 1;
			background-color: rgba(0, 0, 0, 0.7);
			transform: scale(1,1);
			transition: transform 1s;
			.team-name {
				font-size: 1.5em;
				font-weight: bold;
				opacity: 0.75;
				margin-top: 10px;
			}
			.score-value {
				font-size: 4em;
				font-weight: bold;
			}
		}
		.leader {
			z-index: 2;
			transform: scale(1.1,1.1);
		}
		.active {
			animation-name: active;
    		animation-duration: 1s;
			animation-iteration-count: infinite;
		}
		.correct {
			background-color: #33ff33;
			color:#003300;
		}
		.incorrect {
			background-color: #ff3333;
			color:#660000;
		}
		@keyframes active {
			50% {
				background-color: #fff;
				color: black;
			}
		}
	}


	.fade-enter-active, .fade-leave-active {
		transition: all 1s
	}
	.fade-enter, .fade-leave-active {
		opacity: 0;
	}

	.move-enter-active, .move-leave-active {
		transition: all 1s
	}
	.move-enter {
		transform: translateX(-110%) rotate(-10deg);
	}
	.move-leave-active {
		transform: translateX(110%) rotate(10deg);;
	}

	.drop-enter-active, .drop-leave-active {
		transition: all 1s
	}
	.drop-enter, .drop-leave-active {
		transform: translateY(-150%);
	}
</style>
