<script>
	let quizData = [
		{
			question: 'The capital of France is ___________.',
			answer: 'Paris'
		},
		{
			question: 'The largest planet in our solar system is ___________.',
			answer: 'Jupiter'
		},
		{
			question: 'The author of the book "To Kill a Mockingbird" is ___________.',
			answer: 'Harper Lee'
		}
	];

	let currentQuestion = 0;
	let userAnswer = '';
	let score = 0;

	function checkAnswer() {
		if (userAnswer.toLowerCase() === quizData[currentQuestion].answer.toLowerCase()) {
			score++;
			alert('Correct!');
		} else {
			alert(`Sorry, the correct answer is ${quizData[currentQuestion].answer}.`);
		}
		userAnswer = '';
		currentQuestion++;
		if (currentQuestion >= quizData.length) {
			alert(`Quiz finished! Your final score is ${score} out of ${quizData.length}.`);
			currentQuestion = 0;
			score = 0;
			shuffleQuizData();
		}
	}

	function shuffleQuizData() {
		quizData = quizData.map((a) => ({ ...a })).sort(() => Math.random() - 0.5);
	}

	function handleKeyDown(event) {
		if (event.key === 'Enter') {
			checkAnswer();
		}
	}
</script>

<h1>Fill-in-the-Blank Quiz</h1>

{#if currentQuestion < quizData.length}
	<p>{quizData[currentQuestion].question}</p>
	<input type="text" bind:value={userAnswer} on:keydown={handleKeyDown} />
	<button on:click={checkAnswer}>Submit</button>
	<p>{quizData[currentQuestion].answer}</p>
	<p>Score: {score} out of {quizData.length}</p>
	<button on:click={shuffleQuizData}>Restart</button>
{:else}
	<p>Quiz finished! Your final score is {score} out of {quizData.length}.</p>
	<button on:click={shuffleQuizData}>Play Again</button>
{/if}

<style>
	input[type='text'] {
		width: 50%;
		height: 30px;
		font-size: 24px;
		padding: 10px;
	}

	button {
		width: 100px;
		height: 40px;
		font-size: 24px;
		background-color: #4caf50;
		color: #fff;
		border: none;
		border-radius: 5px;
		cursor: pointer;
	}

	button:hover {
		background-color: #3e8e41;
	}
</style>
