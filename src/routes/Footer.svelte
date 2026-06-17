<footer>
	<div class="inner">
		<span class="brand">TILTED<em>CRAFT</em></span>
		<p class="copy">Minecraft Vanilla++ · Fabric 1.20.1</p>
		<a href="https://discord.gg/VOTRE_LIEN_ICI" target="_blank" rel="noopener noreferrer" class="discord-link">
			<svg width="18" height="14" viewBox="0 0 20 15" fill="none" aria-hidden="true">
				<path d="M16.942.958A16.62 16.62 0 0 0 12.92.003a.062.062 0 0 0-.065.031c-.174.308-.366.71-.502 1.026a15.333 15.333 0 0 0-4.604 0A10.295 10.295 0 0 0 7.245.034a.064.064 0 0 0-.066-.031A16.584 16.584 0 0 0 3.158.958a.058.058 0 0 0-.027.023C.48 5.392-.287 9.727.089 14.008a.067.067 0 0 0 .026.046 16.703 16.703 0 0 0 5.031 2.546.065.065 0 0 0 .07-.023c.388-.529.733-1.087 1.029-1.675a.063.063 0 0 0-.035-.088 10.993 10.993 0 0 1-1.572-.749.064.064 0 0 1-.006-.106c.106-.079.211-.16.312-.243a.061.061 0 0 1 .064-.01c3.298 1.506 6.869 1.506 10.13 0a.061.061 0 0 1 .065.009c.1.083.205.165.312.244a.064.064 0 0 1-.006.106 10.36 10.36 0 0 1-1.572.748.063.063 0 0 0-.034.089c.3.587.645 1.145 1.028 1.674a.064.064 0 0 0 .07.023 16.664 16.664 0 0 0 5.038-2.546.065.065 0 0 0 .026-.045c.421-4.358-.707-8.142-2.993-11.49a.051.051 0 0 0-.026-.023ZM6.684 11.594c-.992 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.803 2.031-1.81 2.031Zm6.69 0c-.993 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.795 2.031-1.81 2.031Z" fill="currentColor"/>
			</svg>
			Discord
		</a>
	</div>
</footer>

<style>
	footer {
		border-top: 1px solid rgba(255,255,255,0.05);
		background: rgba(5,5,8,0.95);
		padding: 2rem 0;
		position: relative;
		z-index: 1;
	}

	.inner {
		max-width: 1160px;
		margin: 0 auto;
		padding: 0 clamp(1.2rem, 4vw, 2.5rem);
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.brand {
		font-family: 'Space Grotesk', system-ui, sans-serif;
		font-weight: 700;
		font-size: 1.05rem;
		letter-spacing: -0.02em;
		color: rgba(240,240,240,0.9);
	}

	.brand em {
		font-style: normal;
		color: #E8A83A;
	}

	.copy {
		font-family: 'Space Mono', monospace;
		font-size: 0.72rem;
		color: rgba(240,240,240,0.3);
		letter-spacing: 0.08em;
	}

	.discord-link {
		display: flex;
		align-items: center;
		gap: 0.45rem;
		font-family: 'Space Grotesk', system-ui, sans-serif;
		font-size: 0.85rem;
		font-weight: 600;
		color: rgba(240,240,240,0.5);
		transition: color 0.2s;
	}

	.discord-link:hover {
		color: #5865F2;
	}
</style>
