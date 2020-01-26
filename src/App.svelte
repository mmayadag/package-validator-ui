<script>
	import Box from './components/Box/Box.svelte';
	import Repo from "./pages/Repo.svelte";
	export let Title;
</script>


<style>
	main  , header{
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3rem;
		font-weight: 100;
	}
	.cardContainer{
		display: flex;
		align-items: center;
		justify-content: space-evenly;
	}

	@media (min-width: 640px) {
		main , header {
			max-width: none;
		}
	}
	@media (max-width: 640px) {
		main , header {
			max-width: 100%;
		}
		h1 {
			font-size: 2em;
		}
	}
	
</style>
<header>
	<div class="cardContainer">
		<Box alt="github" image="github1.svg" title="github" />
		<Box alt="npm" image="npm.svg" title="package.json" />
		<Box alt="composer" image="packageist.png" title="composer.json" />
	</div>
	<h1>{Title}!</h1>
</header>
<main class="raw-html">

<Repo />
</main>