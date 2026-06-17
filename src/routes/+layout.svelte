<script>
	import Header from './Header.svelte';
	import Footer from './Footer.svelte';
	import Canvas from './canvas.svelte';
	import './style.css';
</script>

<Canvas />
<Header />

<main>
	<slot />
</main>

<Footer />

<style>
	main {
		min-height: 100vh;
		position: relative;
		z-index: 1;
	}
</style>
