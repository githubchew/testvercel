<script>
	import { onMount } from 'svelte';

	const words = [
		'monster',
		'ghost',
		'zombie',
		'goblin',
		'vampire',
		'apple',
		'food',
		'cool',
		'cat',
		'books',
		'cherry',
		'dog',
		'fish',
		'happy',
		'eighty',
		'japan',
		'milk',
		'mango',
		'pizza',
		'sugar',
		'water',
		'orange',
		'pizza',
		'cake',
		'pancake',
		'mary',
		'jerry',
		'ferry',
		'city',
		'pizza',
		'pizza'
	];
	const monsterEmojis = ['🧟‍♂️', '👹', '👻', '🐉', '🦇', '🧛‍♂️', '👺', '💀'];
	const winningEmojis = ['🎉', '🏆', '🌟', '🥳', '👏']; // Array of winning emojis

	let currentIndex = 0;
	let targetWord = words[currentIndex];
	let userInput = '';
	let monsterSize = 100;
	let monsterCount = 0; // Start with no locked monsters
	let wrongAttempts = 0;
	let correctCount = 0;
	let score = 0;
	let feedbackMessage = '';

	let roundTime = 2; // Default round time in minutes
	let countdown; // To hold the interval for countdown
	let timeLeft; // To track remaining time

	function getRandomMonster() {
		return monsterEmojis[Math.floor(Math.random() * monsterEmojis.length)];
	}

	function getRandomWinningEmoji() {
		return winningEmojis[Math.floor(Math.random() * winningEmojis.length)];
	}

	function handleInput(event) {
		userInput = event.target.value;

		if (userInput === targetWord) {
			feedbackMessage = `Correct! ${getRandomWinningEmoji()}`; // Show a random winning emoji
			score++;
			correctCount++;
			monsterSize = Math.max(50, monsterSize - 10);
			moveToNextWord();

			// Add a monster behind bars after a correct word
			monsterCount++;

			if (correctCount >= 2) {
				monsterCount = Math.max(1, monsterCount - 1);
				correctCount = 0;
			}
		} else {
			feedbackMessage = 'Try again!';
			wrongAttempts++;

			if (wrongAttempts >= 5) {
				monsterCount = Math.min(10, monsterCount + 1);
			}
			monsterSize = Math.min(200, monsterSize + 10);
		}
	}

	function moveToNextWord() {
		currentIndex++;
		if (currentIndex < words.length) {
			targetWord = words[currentIndex];
			resetInput();
		} else {
			feedbackMessage = `Great job! You've completed all words. Final Score: ${score}`;
			clearInterval(countdown); // Stop the countdown
			setTimeout(resetGame, 3000); // Wait 3 seconds before resetting
		}
	}

	function resetInput() {
		userInput = '';
		monsterSize = 100;
		wrongAttempts = 0;
	}

	function resetGame() {
		currentIndex = 0;
		targetWord = words[currentIndex];
		userInput = '';
		monsterSize = 100;
		monsterCount = 0; // Reset to no locked monsters
		wrongAttempts = 0;
		correctCount = 0;
		score = 0;
		feedbackMessage = '';
		clearInterval(countdown); // Stop the countdown
		startCountdown(); // Restart countdown for new game
	}

	function startCountdown() {
		timeLeft = roundTime * 60; // Convert minutes to seconds
		countdown = setInterval(() => {
			timeLeft--;
			if (timeLeft <= 0) {
				clearInterval(countdown);
				feedbackMessage = `Time's up! Final Score: ${score}`;
				setTimeout(resetGame, 3000); // Wait 3 seconds before resetting
			}
		}, 1000);
	}

	onMount(() => {
		startCountdown(); // Start the countdown when component mounts
	});
</script>

<div>
	<h1>Type the Word!</h1>

	<div class="bars">
		<div class="monster-container">
			{#each Array(monsterCount) as _}
				<!-- Display locked-up monsters -->
				<div class="locked-monster">{getRandomMonster()}</div>
			{/each}
		</div>
	</div>

	<p>Word to type: <strong>{targetWord}</strong></p>

	<input type="text" bind:value={userInput} on:input={handleInput} />
	<button on:click={resetGame}>Reset</button>

	<div class="monster-container">
		{#each Array(monsterCount) as _, i}
			<div class="monster" style="font-size: {monsterSize}px;" role="img" aria-label="monster">
				{getRandomMonster()}
			</div>
		{/each}
	</div>

	<div class="trophy-container">
		{#each Array(score) as _}
			<span role="img" aria-label="trophy">🏆</span>
		{/each}
	</div>

	<div class="slider">
		<label for="roundTime">Set Round Time: {roundTime} minutes</label>
		<input type="range" id="roundTime" min="1" max="5" bind:value={roundTime} step="1" />
	</div>

	{#if feedbackMessage}
		<div class="feedback">{feedbackMessage}</div>
	{/if}
</div>

<style>
	body {
		background-color: #111; /* Constant dark background */
		color: #fff;
		font-family: 'Creepster', cursive;
		text-align: center;
		padding: 20px;
		position: relative;
	}

	h1 {
		color: orange;
		text-shadow: 2px 2px 5px black;
		margin-bottom: 20px;
	}

	p {
		font-size: 1.5em;
		margin: 20px 0;
		margin-top: 120px;
	}

	input {
		padding: 10px;
		font-size: 1.2em;
		border: 2px solid orange;
		border-radius: 5px;
		background-color: #333;
		color: #fff;
		transition: border 0.3s;
	}

	input:focus {
		border: 2px solid #ffcc00;
		outline: none;
	}

	button {
		padding: 10px 20px;
		font-size: 1.2em;
		background-color: #ff4500;
		border: none;
		border-radius: 5px;
		color: white;
		cursor: pointer;
		transition: background-color 0.3s;
	}

	button:hover {
		background-color: #ff6347;
	}

	.monster-container {
		display: flex;
		justify-content: center;
		margin-top: 20px;
	}

	.monster {
		font-size: var(--monster-size);
		transition: font-size 0.2s;
		animation: shake 0.5s;
	}

	.locked-monster {
		font-size: 60px;
		z-index: 1; /* In front of the bars */
	}

	.bars {
		position: absolute;
		top: 0;
		left: 50%;
		transform: translateX(-50%);
		width: 100%;
		height: 100px;
		background-color: rgba(0, 0, 0, 0.7);
		border: 5px solid #ffcc00;
		border-radius: 5px;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.feedback {
		margin-top: 20px;
		font-weight: bold;
		font-size: 1.5em;
		color: #ffcc00;
		text-shadow: 1px 1px 2px black;
		position: absolute;
		top: 80px; /* Position above the input */
		left: 50%;
		transform: translateX(-50%);
	}

	.trophy-container {
		margin-top: 20px;
		font-size: 2em;
	}

	.slider {
		margin: 20px 0;
	}

	@keyframes shake {
		0% {
			transform: translate(0);
		}
		25% {
			transform: translate(-5px);
		}
		50% {
			transform: translate(5px);
		}
		75% {
			transform: translate(-5px);
		}
		100% {
			transform: translate(0);
		}
	}
</style>
