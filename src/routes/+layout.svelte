<script>
	import Header from '$lib/components/header/Header.svelte';
	import '$lib/styles/style.css';
</script>

<Header />

<main>
	<slot />
</main>

<style>
	main {
		padding: 2rem;
	}
</style>
