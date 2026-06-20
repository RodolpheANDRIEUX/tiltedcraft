<script>
	const DISCORD = 'https://discord.gg/SGYGyHRm';
</script>

<header>
	<div class="inner">
		<a href="/" class="wordmark">
			TILTED<span class="accent">CRAFT</span>
		</a>

		<a href={DISCORD} target="_blank" rel="noopener noreferrer" class="btn-discord">
			<svg width="20" height="15" viewBox="0 0 20 15" fill="none" aria-hidden="true">
				<path d="M16.942.958A16.62 16.62 0 0 0 12.92.003a.062.062 0 0 0-.065.031c-.174.308-.366.71-.502 1.026a15.333 15.333 0 0 0-4.604 0A10.295 10.295 0 0 0 7.245.034a.064.064 0 0 0-.066-.031A16.584 16.584 0 0 0 3.158.958a.058.058 0 0 0-.027.023C.48 5.392-.287 9.727.089 14.008a.067.067 0 0 0 .026.046 16.703 16.703 0 0 0 5.031 2.546.065.065 0 0 0 .07-.023c.388-.529.733-1.087 1.029-1.675a.063.063 0 0 0-.035-.088 10.993 10.993 0 0 1-1.572-.749.064.064 0 0 1-.006-.106c.106-.079.211-.16.312-.243a.061.061 0 0 1 .064-.01c3.298 1.506 6.869 1.506 10.13 0a.061.061 0 0 1 .065.009c.1.083.205.165.312.244a.064.064 0 0 1-.006.106 10.36 10.36 0 0 1-1.572.748.063.063 0 0 0-.034.089c.3.587.645 1.145 1.028 1.674a.064.064 0 0 0 .07.023 16.664 16.664 0 0 0 5.038-2.546.065.065 0 0 0 .026-.045c.421-4.358-.707-8.142-2.993-11.49a.051.051 0 0 0-.026-.023ZM6.684 11.594c-.992 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.803 2.031-1.81 2.031Zm6.69 0c-.993 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.795 2.031-1.81 2.031Z" fill="currentColor"/>
			</svg>
			Rejoindre
		</a>
	</div>
</header>

<style>
	header {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		z-index: 100;
		padding: 1.1rem 0;
		background: linear-gradient(to bottom, rgba(5,5,8,0.92) 0%, transparent 100%);
		backdrop-filter: blur(6px);
		-webkit-backdrop-filter: blur(6px);
	}

	.inner {
		max-width: 1160px;
		margin: 0 auto;
		padding: 0 clamp(1.2rem, 4vw, 2.5rem);
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.wordmark {
		font-family: 'Space Grotesk', system-ui, sans-serif;
		font-weight: 700;
		font-size: 1.2rem;
		letter-spacing: -0.02em;
		color: #F0F0F0;
	}

	.accent {
		color: #E8A83A;
	}
</style>
