<template>
	<form
		@submit.prevent="sendTweet()"
		class="tweet-bar"
	>
		<div class="tweet-input">
			<img
				src="@/assets/profile.webp"
				alt="profile-icon"
			/>
			<input
				type="text"
				placeholder="Quoi de neuf ?"
				v-model="tweet.content"
			/>
		</div>
		<div class="tweet-buttons">
			<div class="tweet-images">
				<img
					src="@/assets/icons/pictures.svg"
					alt="pictures"
				/>
				<img
					src="@/assets/icons/gif.svg"
					alt="gif"
				/>
				<img
					src="@/assets/icons/question.svg"
					alt="question"
				/>
				<img
					src="@/assets/icons/emoji.svg"
					alt="emoji"
				/>
				<img
					src="@/assets/icons/calendar.svg"
					alt="calendar"
				/>
				<img
					src="@/assets/icons/position.svg"
					alt="position"
				/>
			</div>
			<div>
				<button type="submit">Tweeter</button>
			</div>
		</div>
	</form>
</template>
<script>
	export default {
		name: 'PostBar',
		data() {
			return {
				tweet: {
					name: `Coding & IOT`,
					username: '@etudiant.e',
					time: null,
					content: '',
					comments: Math.floor(Math.random() * 1000),
					retweets: Math.floor(Math.random() * 1000),
					likes: Math.floor(Math.random() * 1000),
				},
				possibles: [
					'red',
					'green',
					'blue',
					'yellow',
					'orange',
					'purple',
					'magenta',
					'cyan',
					'pink',
					'white',
					'clignote',
					'breath',
					'load',
					'snake',
					'switch',
					'fill',
					'love',
					'friend',
					'beer'
				]
			}
		},

		methods: {
			sendTweet() {
				if (this.tweet.content === '') return
				this.checkHashtag(this.tweet)
				fetch(process.env.VUE_APP_API_DSN + '/tweets', {
					method: 'POST',
					headers: {
						'Content-Type': 'application/json',
					},
					body: JSON.stringify(this.tweet),
				})
					.then((response) => response.json())
					.then((data) => {
						this.$socket.emit('tweet', data)
						this.tweet.content = ''
					})
			},
			checkHashtag(tweet) {
				if (tweet.content.includes('#')) {
					const hashtags = tweet.content.match(/#[a-zA-Z0-9]+/g)
					const hashtagsWithoutHash = hashtags.map((hashtag) => hashtag.replace('#', ''))
					const hashtagsInPossibles = hashtagsWithoutHash.filter((hashtag) => this.possibles.includes(hashtag))

					if (hashtagsInPossibles.length > 0) {
						console.log(hashtagsInPossibles)
						this.$socket.emit('hashtags', hashtagsInPossibles)
					}
				}
			},
		},
	}
</script>
<style scoped>
	.tweet-bar {
		display: flex;
		flex-direction: column;
		border-bottom: 1px solid #717171;
	}

	.tweet-input {
		display: flex;
		align-items: center;
		padding: 20px;
	}

	.tweet-input img {
		width: 50px;
		border-radius: 50%;
	}

	.tweet-input input {
		padding: 0 20px;
		background-color: transparent;
		width: 100%;
		border: none;
		color: white;
		font-size: 20px;
	}

	.tweet-input input:focus {
		outline: none;
	}

	.tweet-buttons {
		display: flex;
		align-items: center;
		justify-content: space-between;
		width: 100%;
		padding: 0 10px 20px 50px;
	}

	.tweet-images {
		display: flex;
		align-items: center;
		gap: 10px;
		margin-left: 40px;
	}

	.tweet-images img {
		width: 20px;
		margin-right: 10px;
		filter: invert(1);
	}

	.tweet-buttons button {
		background-color: #1da1f2;
		color: white;
		border: none;
		border-radius: 20px;
		padding: 10px 20px;
		font-weight: bold;
	}
</style>
