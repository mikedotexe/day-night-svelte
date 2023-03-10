<script>
	import './styles.css';
</script>

<div class="app">
	<main>
		<slot />
	</main>
</div>

<style>
	.app {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		padding: 1rem;
		width: 100%;
		max-width: 64rem;
		margin: 0 auto;
		box-sizing: border-box;
	}

	:global(body) {
		background-color: #336699;
		transition: background-color 1s ease-out;
	}

	:global(body.night) {
		/* Taken from a zoomed-in pixel on CronCat logo */
		background-color: #333333;
		transition: background-color 1s ease-out;
	}
</style>
