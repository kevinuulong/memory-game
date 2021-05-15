<script>
	let game = {
		started: false,
		sequence: new Array(3),
		current: "",
		chances: 3,
		guessing: false,
	};

	function startGame(correct = true) {
		if (game.chances) {
			game.started = true;
			game.sequence = correct ? generateSequence(game.sequence.length + 1) : generateSequence(game.sequence.length - 1);
			cycleGame(game.sequence);
		} else {
			game.started = false;
			game.score = game.sequence.length - 1;
		}
	}

	function cycleGame(sequence) {
		if (sequence.length != 0) {
			setTimeout(() => {
				game.current = sequence[0];
				setTimeout(() => {
					game.current = "";
					cycleGame(sequence.slice(1));
				}, 300);
			}, 600);
		} else game.guessing = true;
	}

	function generateSequence(length) {
		return new Array(length)
			.fill(0)
			.map(() => Math.floor(Math.random() * 10));
	}

	function submitGuess(e) {
		if (e.key === "Enter") {
			e.preventDefault();
			game.guess = Array.from(e.target.value).map((num) => Number(num));
			e.target.value = "";
			handleGuess();
		}
	}

	function handleGuess() {
		game.guessing = false;

		let gameCard = document.querySelector("#gameCard");

		if (arrayEquals(game.guess, game.sequence)) {
			gameCard.classList.add("correct");
			setTimeout(() => {
				gameCard.classList.remove("correct");
				startGame();
			}, 1000);
		} else {
			gameCard.classList.add("incorrect");
			setTimeout(() => {
				game.chances--;
				gameCard.classList.remove("incorrect");
				startGame(false);
			}, 1000);
		}
	}

	function arrayEquals(a, b) {
		return (
			Array.isArray(a) &&
			Array.isArray(b) &&
			a.length === b.length &&
			a.every((val, index) => val === b[index])
		);
	}
</script>

<svelte:head>
	<title>Number Memory Game</title>
</svelte:head>

{#if !game.started}
	<div class="card">
		<img
			src="https://kevinuulong.com/images/logos/kl.svg"
			alt="Kevin Long's logo"
		/>
		<h1>Number Memory Game</h1>
		{#if game.score}
			<p><b>Score: </b>{game.score}</p>
		{/if}
		<div class="button" id="startGame" on:click={startGame}>Start Game</div>
	</div>
{:else}
	<div class="card" id="gameCard">
		{#if !game.guessing}
			{game.current}
		{:else}
			<!-- svelte-ignore a11y-autofocus -->
			<textarea on:keydown={submitGuess} autofocus />
		{/if}
	</div>
{/if}

<style>
	@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600&display=swap");

	:global(body) {
		margin: 0;
		padding: 0;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #f0f0f6;
		user-select: none;
		-webkit-user-drag: none;
	}

	img {
		height: 28px;
	}

	h1 {
		color: #2b2c30;
		font-family: "Montserrat", sans-serif;
		font-weight: 600;
	}

	.button {
		font-family: "Montserrat", sans-serif;
		width: fit-content;
		color: #fff;
		background-color: #2f80ed;
		height: 56px;
		line-height: 56px;
		padding-left: 28px;
		padding-right: 28px;
		border-radius: 14px;
		font-weight: 600;
		font-size: 21px;
		cursor: pointer;
		user-select: none;
		display: inline-block;
	}

	#startGame {
		position: absolute;
		bottom: 28px;
		width: calc(100% - 112px);
		text-align: center;
	}

	.card {
		width: 300px;
		height: 300px;
		padding: 28px;
		background-color: #fff;
		border-radius: 28px;
		position: relative;
		font-family: 'Montserrat', sans-serif;
		color: #2b2c30;
	}

	#gameCard {
		font-size: 200px;
		line-height: 300px;
		text-align: center;
		color: #2b2c30;
		font-family: "Montserrat", sans-serif;
		font-weight: 600;
	}

	#gameCard textarea,
	textarea:focus {
		line-height: 28px;
		font-size: 20px;
		resize: none;
		border: none;
		outline: none;
		background-color: #2b2c30;
		border-radius: 14px;
		width: 100%;
		height: 100%;
		padding: 14px;
		color: #fff;
		font-weight: 400;
	}

	:global(.correct) {
		background-color: #2ddb73 !important;
	}

	:global(.incorrect) {
		background-color: #eb5757 !important;
	}

	b {
		font-weight: 600;
	}
</style>
