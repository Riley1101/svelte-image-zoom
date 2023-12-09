<script>
	import '../app.css';
	import Header from '$lib/common/Header.svelte';
</script>

<Header />
<main class="min-h-[calc(100vh-3em)]">
	<div class="max-w-5xl mx-auto">
		<slot />
	</div>
</main>
