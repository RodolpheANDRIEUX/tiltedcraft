<script>
	import { onMount } from 'svelte';

	const DISCORD = 'https://discord.gg/SGYGyHRm';

	const stats = [
		{ value: 2064,  suffix: '',  label: 'chunks\nrender distance', sub: 'vs. 10 sur un Realm standard' },
		{ value: 144,   suffix: '',  label: 'fps\ngarantis (ou presque)', sub: 'shaders fine-tunés à la main' },
		{ value: 64000, suffix: '',  label: 'blocs\nde map',  sub: 'pré-générée, world border inclus' },
		{ value: 81,   suffix: '+', label: 'mods\nactifs',   sub: 'invisibles — ils perfectionnent' },
	];

	const profiles = [
		{
			icon: '🏗️',
			title: 'Bâtisseur',
			desc: 'Tu vois le monde comme une toile. La distance de vue te laisse construire sans limite visuelle. Le world tour en stream valorise chaque chef-d\'œuvre.',
		},
		{
			icon: '⚡',
			title: 'Conquérant',
			desc: 'Tu veux explorer, progresser, dominer. Une map de 64 000 blocs pré-générée t\'attend. L\'End s\'ouvre après ~2 semaines — sois prêt.',
		},
		{
			icon: '🤝',
			title: 'Compagnon',
			desc: 'Le social avant tout. Des waystones pour se retrouver, des mini-events réguliers, et des coffres sur lesquels sauter à 2h du mat en voc sur Discord.',
		},
		{
			icon: '🌱',
			title: 'Débutant',
			desc: 'Premier serveur ? L\'End est fermé les premières semaines, les waystones t\'évitent de te perdre, et il y a toujours quelqu\'un en voc sur Discord pour t\'aider à t\'installer.',
		},
	];

	const steps = [
		{
			num: '01',
			title: 'Prism Launcher',
			body: 'Télécharge et installe Prism Launcher, puis connecte-toi à ton compte Minecraft.',
			action: { label: 'prismlauncher.org →', href: 'https://prismlauncher.org/' },
		},
		{
			num: '02',
			title: 'Importer le modpack',
			body: 'Clique sur Add Instance → Import → colle l\'URL de ta config. 5 versions selon ton PC',
			configs: [
				{ label: 'Grille-Pain',    href: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/GrillePain/TILTEDCRAFT.-.GrillePain.zip' },
				{ label: 'Lower Mid',      href: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/LowerMid/TILTEDCRAFT.-.LowerMidRange.zip' },
				{ label: 'Mid-range',      href: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/Mid/TILTEDCRAFT.-.MidRange.zip' },
				{ label: 'High End ✦',     href: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/HighEnd/TILTEDCRAFT.-.HighEnd.zip' },
				{ label: 'Master Race',    href: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/MasterRace/TILTEDCRAFT.-.MasterRace.zip' },
			],
		},
		{
			num: '03',
			title: 'Télécharger la map',
			body: 'La map est pré-générée et doit être installée séparément dans ton instance Prism.',
			warning: '139 Go à télécharger — prévoir au moins 150 Go d\'espace libre avant de commencer.',
			mapUrl: 'https://tiltedcraft.fr/.voxy.zip',
			path: 'PrismLauncher\\instances\\TILTEDCRAFT-[config]\\minecraft\\.voxy',
		},
		{
			num: '04',
			title: 'Se connecter',
			body: 'Ajoute ton pseudo dans #whitelist✅ sur Discord, lance l\'instance. TILTEDCRAFT apparaît automatiquement dans Multijoueur.',
		},
	];

	let statsEl;
	let animated = false;

	let copiedUrl = null;
	let highlightStep2 = false;
	let _resetTimer;

	async function copyConfig(href) {
		try {
			await navigator.clipboard.writeText(href);
		} catch {
			// fallback pour navigateurs restrictifs
			const el = document.createElement('textarea');
			el.value = href;
			document.body.appendChild(el);
			el.select();
			document.execCommand('copy');
			document.body.removeChild(el);
		}
		copiedUrl = href;
		highlightStep2 = true;
		clearTimeout(_resetTimer);
		setTimeout(() => { copiedUrl = null; }, 2200);
		_resetTimer = setTimeout(() => { highlightStep2 = false; }, 6000);
	}

	const BETA   = new Date('2026-06-22T18:00:00+02:00');
	const LAUNCH = new Date('2026-06-26T18:00:00+02:00');

	let cd = { d: 0, h: 0, m: 0, s: 0, phase: 'beta' }; // 'beta' | 'official' | 'done'

	function tick() {
		const now = Date.now();
		const target = now < BETA ? BETA : LAUNCH;
		const phase  = now < BETA ? 'beta' : now < LAUNCH ? 'official' : 'done';
		if (phase === 'done') { cd = { ...cd, phase: 'done' }; return; }
		const diff = target - now;
		cd = {
			d: Math.floor(diff / 86400000),
			h: Math.floor((diff % 86400000) / 3600000),
			m: Math.floor((diff % 3600000) / 60000),
			s: Math.floor((diff % 60000) / 1000),
			phase,
		};
	}

	function easeOutCubic(x) { return 1 - Math.pow(1 - x, 3); }

	function animateCount(el, target, duration = 1800) {
		const start = performance.now();
		(function loop(now) {
			const p = Math.min((now - start) / duration, 1);
			el.textContent = Math.round(easeOutCubic(p) * target).toLocaleString('fr-FR');
			if (p < 1) requestAnimationFrame(loop);
		})(start);
	}

	onMount(() => {
		tick();
		const timer = setInterval(tick, 1000);

		const io = new IntersectionObserver(
			(entries) => {
				if (entries[0].isIntersecting && !animated) {
					animated = true;
					document.querySelectorAll('[data-count]').forEach(el => {
						animateCount(el, parseInt(el.dataset.count));
					});
				}
			},
			{ threshold: 0.25 }
		);
		if (statsEl) io.observe(statsEl);

		return () => { clearInterval(timer); io.disconnect(); };
	});
</script>

<svelte:head>
	<title>TILTEDCRAFT — Minecraft comme il aurait dû être</title>
	<meta name="description" content="Serveur Minecraft Vanilla++ — ~81 mods, shaders custom, 144fps, 2064 chunks de render distance." />
	<meta name="robots" content="index, follow" />
</svelte:head>


<!-- ═══════════════════════════════ HERO ═══════════════════════════════ -->
<section class="hero">
	<div class="hero-inner">
		<p class="badge">Minecraft 1.20.1 &nbsp;·&nbsp; Fabric &nbsp;·&nbsp; Vanilla++</p>

		<h1>TILTED<span class="h1-accent">CRAFT</span></h1>

		<p class="tagline">
			Pas un modpack RPG, pas un serveur moddé classique.<br />
			<span class="tagline-em">Juste du Minecraft… comme si Mojang avait sorti Minecraft&nbsp;2.</span>
		</p>

		<!-- COUNTDOWN -->
		{#if cd.phase !== 'done'}
		<div class="countdown" class:cd-official={cd.phase === 'official'}>
			<p class="cd-label">
				{cd.phase === 'beta' ? 'Accès bêta dans' : 'Lancement officiel dans'}
			</p>
			<div class="cd-grid">
				<div class="cd-unit">
					<span class="cd-num">{String(cd.d).padStart(2, '0')}</span>
					<span class="cd-sub">jours</span>
				</div>
				<span class="cd-sep">:</span>
				<div class="cd-unit">
					<span class="cd-num">{String(cd.h).padStart(2, '0')}</span>
					<span class="cd-sub">heures</span>
				</div>
				<span class="cd-sep">:</span>
				<div class="cd-unit">
					<span class="cd-num">{String(cd.m).padStart(2, '0')}</span>
					<span class="cd-sub">min</span>
				</div>
				<span class="cd-sep">:</span>
				<div class="cd-unit">
					<span class="cd-num">{String(cd.s).padStart(2, '0')}</span>
					<span class="cd-sub">sec</span>
				</div>
			</div>
			<p class="cd-date">
				{cd.phase === 'beta' ? '22 · 06 · 2026 — 18h00' : '26 · 06 · 2026 — 18h00'}
			</p>
		</div>
		{/if}

		<a href={DISCORD} target="_blank" rel="noopener noreferrer" class="btn-discord large">
			<svg width="22" height="17" viewBox="0 0 20 15" fill="none" aria-hidden="true">
				<path d="M16.942.958A16.62 16.62 0 0 0 12.92.003a.062.062 0 0 0-.065.031c-.174.308-.366.71-.502 1.026a15.333 15.333 0 0 0-4.604 0A10.295 10.295 0 0 0 7.245.034a.064.064 0 0 0-.066-.031A16.584 16.584 0 0 0 3.158.958a.058.058 0 0 0-.027.023C.48 5.392-.287 9.727.089 14.008a.067.067 0 0 0 .026.046 16.703 16.703 0 0 0 5.031 2.546.065.065 0 0 0 .07-.023c.388-.529.733-1.087 1.029-1.675a.063.063 0 0 0-.035-.088 10.993 10.993 0 0 1-1.572-.749.064.064 0 0 1-.006-.106c.106-.079.211-.16.312-.243a.061.061 0 0 1 .064-.01c3.298 1.506 6.869 1.506 10.13 0a.061.061 0 0 1 .065.009c.1.083.205.165.312.244a.064.064 0 0 1-.006.106 10.36 10.36 0 0 1-1.572.748.063.063 0 0 0-.034.089c.3.587.645 1.145 1.028 1.674a.064.064 0 0 0 .07.023 16.664 16.664 0 0 0 5.038-2.546.065.065 0 0 0 .026-.045c.421-4.358-.707-8.142-2.993-11.49a.051.051 0 0 0-.026-.023ZM6.684 11.594c-.992 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.803 2.031-1.81 2.031Zm6.69 0c-.993 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.795 2.031-1.81 2.031Z" fill="currentColor"/>
			</svg>
			Rejoindre sur Discord
		</a>
	</div>

	<!-- SCROLL INDICATOR -->
	<div class="scroll-cta" aria-hidden="true">
		<span class="scroll-text">Scroll</span>
		<div class="scroll-arrow">
			<svg width="16" height="24" viewBox="0 0 16 24" fill="none">
				<path d="M8 0v20M1 13l7 7 7-7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
			</svg>
		</div>
	</div>
</section>


<!-- ═══════════════════════════════ STATS ════════════════════════════════ -->
<section class="stats-section" bind:this={statsEl}>
	<div class="stats-bg"></div>
	<div class="container">
		<div class="stats-grid">
			{#each stats as s, i}
				<div class="stat reveal" style="animation-delay:{i * 80}ms">
					<div class="stat-number-wrap">
						<span class="stat-num" data-count={s.value}>0</span><span class="stat-suffix">{s.suffix}</span>
					</div>
					<p class="stat-label">{@html s.label.replace('\n', '<br />')}</p>
					<p class="stat-sub">{s.sub}</p>
				</div>
			{/each}
		</div>
	</div>
</section>


<!-- ══════════════════════════════ CONCEPT ═══════════════════════════════ -->
<section class="concept-section">
	<div class="container">
		<div class="concept-grid">
			<div class="concept-left reveal">
				<p class="eyebrow">Le concept</p>
				<h2>Minecraft, comme<br />il aurait dû être</h2>
				<p class="body-copy">
					Un serveur 1.20.1 Fabric tournant avec ~81 mods qui ne changent pas le jeu — ils le <strong>perfectionnent</strong>. L'unique mod véritablement visible ? <strong>Create.</strong> Tout le reste travaille dans l'ombre&nbsp;: rendu, performances, qualité de vie.
				</p>
				<blockquote>
					Vous ne serez pas dépaysé.<br />Juste émerveillé.
				</blockquote>
			</div>

			<ul class="features reveal" style="animation-delay:120ms">
				{#each [
					'Shaders récupérés directement chez leurs développeurs',
					'Fine-tunés à la main — expérience rare',
					'L\'End fermé les 2 premières semaines',
					'Mini-events réguliers accessibles à tous',
					'World tour en stream pour valoriser vos builds',
					'Waystones pour naviguer la map de 64&nbsp;000 blocs',
				] as feat}
					<li>{@html feat}</li>
				{/each}
			</ul>
		</div>
	</div>
</section>


<!-- ══════════════════════════════ CONFIGS ═══════════════════════════════ -->
<section class="configs-section">
	<div class="container">
		<p class="eyebrow text-center reveal">Accessible à tous</p>
		<h2 class="section-title text-center reveal">5 configs, zéro excuse</h2>

		<div class="configs-grid">
			{#each [
				{ cls: 'config-toast', rank: 'GRILLE-PAIN', icon: '🍞', title: 'Grille-Pain', desc: 'Tourne sur un vieux laptop qui ne supportait même pas le vanilla. Vraiment.', tags: ['Ultra léger', 'Vieux PC ✓'], url: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/GrillePain/TILTEDCRAFT.-.GrillePain.zip' },
				{ cls: 'config-lower', rank: 'LOWER MID',   icon: '🖥️', title: 'Lower Mid',   desc: "Un PC d'il y a quelques années ? Cette config est faite pour toi.",          tags: ['Léger', 'Stable'],         url: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/LowerMid/TILTEDCRAFT.-.LowerMidRange.zip' },
				{ cls: 'config-mid',   rank: 'MID-RANGE',   icon: '⚡', title: 'Mid-range',   desc: 'Performances équilibrées, shaders optimisés. Jouer bien sans sacrifier trop le visuel.', tags: ['Équilibré', 'Shaders opt.'], url: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/Mid/TILTEDCRAFT.-.MidRange.zip' },
				{ cls: 'config-high',  rank: 'HIGH END',    icon: '💎', title: 'High End',    desc: 'Quasi-max sans concession. Pour les bonnes machines pour le jeu.',             tags: ['Good fps', 'Good Shaders'], url: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/HighEnd/TILTEDCRAFT.-.HighEnd.zip',     recommended: true },
				{ cls: 'config-master',rank: 'MASTER RACE', icon: '🚀', title: 'Master Race', desc: "3 fps · Shaders full · Render distance maximale. Si t'as pas une 5090 tu peux appeler les pompiers.", tags: ['3 fps', 'Full shaders'], url: 'https://github.com/RodolpheANDRIEUX/tiltedcraft/releases/download/MasterRace/TILTEDCRAFT.-.MasterRace.zip' },
			] as c, i}
				<div
					class="config-card {c.cls} reveal"
					style="animation-delay:{i * 60}ms"
					role="button"
					tabindex="0"
					aria-label="Copier le lien {c.title}"
					on:click={() => copyConfig(c.url)}
					on:keydown={e => e.key === 'Enter' && copyConfig(c.url)}
				>
					{#if c.recommended}<div class="badge-recommended">Recommandé</div>{/if}
					<div class="config-rank">{c.rank}</div>
					<div class="config-icon-big">{c.icon}</div>
					<h3>{c.title}</h3>
					<p>{c.desc}</p>
					<div class="config-tags">{#each c.tags as t}<span>{t}</span>{/each}</div>

					{#if copiedUrl === c.url}
					<div class="card-copied-overlay">
						<span class="card-copied-check">✓</span>
						<p class="card-copied-main">Lien copié</p>
						<p class="card-copied-hint">À coller dans Add Instance → Import</p>
					</div>
					{/if}
				</div>
			{/each}
		</div>
	</div>
</section>


<!-- ══════════════════════════════ PROFILES ══════════════════════════════ -->
<section class="profiles-section">
	<div class="container">
		<p class="eyebrow text-center reveal">Pour tout le monde</p>
		<h2 class="section-title text-center reveal">Ton profil de joueur</h2>

		<div class="profiles-grid">
			{#each profiles as p, i}
				<div class="profile-card reveal" style="animation-delay:{i * 70}ms">
					<span class="profile-icon">{p.icon}</span>
					<h3>{p.title}</h3>
					<p>{p.desc}</p>
				</div>
			{/each}
		</div>
	</div>
</section>


<!-- ════════════════════════════════ JOIN ════════════════════════════════ -->
<section class="join-section">
	<div class="container">
		<p class="eyebrow text-center reveal">C'est l'heure</p>
		<h2 class="section-title text-center reveal">Rejoindre en 3 étapes</h2>

		<div class="steps">
			{#each steps as s, i}
				<div class="step reveal" class:step-highlight={highlightStep2 && i === 1} style="animation-delay:{i * 100}ms">
					<div class="step-num-wrap">
						<span class="step-num">{s.num}</span>
						{#if i < steps.length - 1}
							<div class="step-line"></div>
						{/if}
					</div>
					<div class="step-content">
						<h3>{s.title}</h3>
						<p>{s.body}</p>
						{#if s.warning}
							<div class="step-warning">
								<svg width="16" height="16" viewBox="0 0 24 24" fill="none" aria-hidden="true">
									<path d="M12 2L2 20h20L12 2Z" stroke="currentColor" stroke-width="1.8" stroke-linejoin="round"/>
									<path d="M12 9v5M12 17.5v.5" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/>
								</svg>
								{s.warning}
							</div>
						{/if}
						{#if s.mapUrl}
							<a href={s.mapUrl} class="step-map-dl" download>
								<svg width="15" height="15" viewBox="0 0 24 24" fill="none" aria-hidden="true">
									<path d="M12 3v13M6 11l6 6 6-6M3 20h18" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
								</svg>
								Télécharger .voxy.zip <span class="map-size">139 Go</span>
							</a>
							<p class="step-install-hint">Une fois décompressé, place le dossier <code>.voxy</code> ici&nbsp;:</p>
							<code class="step-path">{s.path}</code>
						{/if}
						{#if s.action}
							<a href={s.action.href} target="_blank" rel="noopener noreferrer" class="step-link">{s.action.label}</a>
						{/if}
						{#if s.configs}
							<div class="step-configs">
								{#each s.configs as cfg}
									<button
										class="config-dl"
										class:copied={copiedUrl === cfg.href}
										on:click={() => copyConfig(cfg.href)}
										title="Copier le lien"
									>
										{#if copiedUrl === cfg.href}
											<span class="copy-icon">✓</span> Copié
										{:else}
											{cfg.label}
										{/if}
									</button>
								{/each}
							</div>
						{/if}
					</div>
				</div>
			{/each}
		</div>
	</div>
</section>


<!-- ══════════════════════════════ FINAL CTA ═════════════════════════════ -->
<section class="cta-section">
	<div class="cta-glow" aria-hidden="true"></div>
	<div class="container cta-inner">
		<p class="cta-hint">Un problème ? Demande à <code>@Tilted bot</code> sur le Discord.</p>
		<h2 class="cta-title reveal">Prêt à jouer ?</h2>
		<a href={DISCORD} target="_blank" rel="noopener noreferrer" class="btn-discord large reveal">
			<svg width="22" height="17" viewBox="0 0 20 15" fill="none" aria-hidden="true">
				<path d="M16.942.958A16.62 16.62 0 0 0 12.92.003a.062.062 0 0 0-.065.031c-.174.308-.366.71-.502 1.026a15.333 15.333 0 0 0-4.604 0A10.295 10.295 0 0 0 7.245.034a.064.064 0 0 0-.066-.031A16.584 16.584 0 0 0 3.158.958a.058.058 0 0 0-.027.023C.48 5.392-.287 9.727.089 14.008a.067.067 0 0 0 .026.046 16.703 16.703 0 0 0 5.031 2.546.065.065 0 0 0 .07-.023c.388-.529.733-1.087 1.029-1.675a.063.063 0 0 0-.035-.088 10.993 10.993 0 0 1-1.572-.749.064.064 0 0 1-.006-.106c.106-.079.211-.16.312-.243a.061.061 0 0 1 .064-.01c3.298 1.506 6.869 1.506 10.13 0a.061.061 0 0 1 .065.009c.1.083.205.165.312.244a.064.064 0 0 1-.006.106 10.36 10.36 0 0 1-1.572.748.063.063 0 0 0-.034.089c.3.587.645 1.145 1.028 1.674a.064.064 0 0 0 .07.023 16.664 16.664 0 0 0 5.038-2.546.065.065 0 0 0 .026-.045c.421-4.358-.707-8.142-2.993-11.49a.051.051 0 0 0-.026-.023ZM6.684 11.594c-.992 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.803 2.031-1.81 2.031Zm6.69 0c-.993 0-1.81-.912-1.81-2.03 0-1.12.802-2.031 1.81-2.031 1.015 0 1.824.918 1.81 2.03 0 1.12-.795 2.031-1.81 2.031Z" fill="currentColor"/>
			</svg>
			Rejoindre le Discord
		</a>
	</div>
</section>


<style>
/* ─────────────────── REVEAL ANIMATION ─────────────────── */
@keyframes fadeUp {
	from { opacity: 0; transform: translateY(20px); }
	to   { opacity: 1; transform: translateY(0); }
}

.reveal {
	animation: fadeUp 0.6s ease both;
}


/* ─────────────────────────── HERO ──────────────────────── */
.hero {
	min-height: 100vh;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	text-align: center;
	padding: 8rem var(--gap) 6rem;
	position: relative;
}

.hero-inner {
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 0;
}

.badge {
	font-family: var(--font-mono);
	font-size: 0.7rem;
	letter-spacing: 0.14em;
	text-transform: uppercase;
	color: var(--gold);
	border: 1px solid var(--gold-dim);
	padding: 0.38rem 1.1rem;
	border-radius: 2rem;
	margin-bottom: 2.5rem;
	display: inline-block;
}

.hero h1 {
	font-size: clamp(2.8rem, 9vw, 9.5rem);
	font-weight: 800;
	line-height: 1;
	letter-spacing: -0.04em;
	text-transform: uppercase;
	margin-bottom: 2rem;
	color: var(--white);
	white-space: nowrap;
}

.h1-accent {
	display: inline;
	background: linear-gradient(120deg, #E8A83A 20%, #FFD97D 50%, #E8A83A 80%);
	background-size: 200% auto;
	-webkit-background-clip: text;
	-webkit-text-fill-color: transparent;
	background-clip: text;
	animation: shimmer 3.5s linear infinite;
}

@keyframes shimmer {
	from { background-position: 0% center; }
	to   { background-position: 200% center; }
}

.tagline {
	font-size: clamp(1rem, 2.2vw, 1.2rem);
	color: var(--white-dim);
	line-height: 1.7;
	max-width: 600px;
	margin-bottom: 2.5rem;
}

.tagline-em {
	color: var(--white);
	font-weight: 500;
}

/* ── countdown ── */
.countdown {
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 0.6rem;
	margin: 1.8rem 0 2rem;
	padding: 1.6rem 2.5rem;
	border: 1px solid rgba(232,168,58,0.2);
	border-radius: 1rem;
	background: rgba(232,168,58,0.04);
	backdrop-filter: blur(4px);
}

.cd-label {
	font-family: var(--font-mono);
	font-size: 0.68rem;
	letter-spacing: 0.18em;
	text-transform: uppercase;
	color: var(--gold);
}

.cd-official .cd-label { color: var(--blue); }
.cd-official { border-color: rgba(74,143,231,0.25); background: rgba(74,143,231,0.05); }
.cd-official .cd-sep  { animation: none; opacity: 0.4; }

.cd-grid {
	display: flex;
	align-items: center;
	gap: 0.5rem;
}

.cd-unit {
	display: flex;
	flex-direction: column;
	align-items: center;
	min-width: 3.5rem;
}

.cd-num {
	font-family: var(--font-mono);
	font-size: clamp(2.2rem, 5vw, 3.5rem);
	font-weight: 700;
	line-height: 1;
	color: var(--white);
}

.cd-sub {
	font-family: var(--font-mono);
	font-size: 0.6rem;
	letter-spacing: 0.12em;
	text-transform: uppercase;
	color: var(--muted);
	margin-top: 0.25rem;
}

.cd-sep {
	font-family: var(--font-mono);
	font-size: clamp(2rem, 4vw, 3rem);
	font-weight: 700;
	color: var(--gold);
	line-height: 1;
	margin-bottom: 1rem;
	opacity: 0.5;
	animation: blink 1s step-end infinite;
}

@keyframes blink {
	0%, 100% { opacity: 0.5; }
	50%       { opacity: 0.1; }
}

.cd-date {
	font-family: var(--font-mono);
	font-size: 0.7rem;
	letter-spacing: 0.1em;
	color: var(--muted);
}

/* ── scroll indicator ── */
.scroll-cta {
	position: absolute;
	bottom: 2rem;
	left: 50%;
	transform: translateX(-50%);
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 0.4rem;
	color: rgba(240,240,240,0.35);
	animation: scroll-fade 2.5s ease-in-out infinite;
}

.scroll-text {
	font-family: var(--font-mono);
	font-size: 0.62rem;
	letter-spacing: 0.2em;
	text-transform: uppercase;
}

.scroll-arrow {
	animation: scroll-bounce 2.5s ease-in-out infinite;
}

@keyframes scroll-fade {
	0%, 100% { opacity: 0.4; }
	50%       { opacity: 0.9; }
}

@keyframes scroll-bounce {
	0%, 100% { transform: translateY(0); }
	50%       { transform: translateY(5px); }
}


/* ─────────────────────────── STATS ─────────────────────── */
.stats-section {
	padding: 7rem 0;
	position: relative;
}

.stats-bg {
	position: absolute;
	inset: 0;
	background: linear-gradient(to bottom, transparent 0%, rgba(12,12,22,0.96) 20%, rgba(12,12,22,0.96) 80%, transparent 100%);
	pointer-events: none;
}

.stats-grid {
	display: grid;
	grid-template-columns: repeat(4, 1fr);
	gap: 1px;
	border: 1px solid var(--border);
	border-radius: 1rem;
	overflow: hidden;
	background: var(--border);
}

.stat {
	background: rgba(12,12,22,0.85);
	padding: 2.8rem 2rem;
	text-align: center;
}

.stat-number-wrap {
	display: flex;
	align-items: baseline;
	justify-content: center;
	gap: 0.1em;
	margin-bottom: 0.6rem;
}

.stat-num {
	font-family: var(--font-mono);
	font-size: clamp(2.4rem, 5vw, 3.8rem);
	font-weight: 700;
	color: var(--gold);
	line-height: 1;
}

.stat-suffix {
	font-family: var(--font-mono);
	font-size: clamp(1.6rem, 3vw, 2.4rem);
	color: var(--gold);
	font-weight: 700;
}

.stat-label {
	font-size: 0.88rem;
	font-weight: 600;
	text-transform: uppercase;
	letter-spacing: 0.08em;
	color: var(--white);
	margin-bottom: 0.5rem;
	line-height: 1.4;
}

.stat-sub {
	font-family: var(--font-mono);
	font-size: 0.68rem;
	color: var(--muted);
	letter-spacing: 0.04em;
}


/* ────────────────────────── CONCEPT ────────────────────── */
.concept-section {
	padding: 8rem 0;
	background: linear-gradient(to bottom, transparent, rgba(8,8,16,0.97), rgba(8,8,16,0.97), transparent);
}

.concept-grid {
	display: grid;
	grid-template-columns: 1fr 1fr;
	gap: clamp(3rem, 6vw, 6rem);
	align-items: start;
}

.concept-left h2 {
	font-size: clamp(2.2rem, 4.5vw, 3.5rem);
	margin-bottom: 1.5rem;
}

.body-copy {
	color: var(--white-dim);
	font-size: 1.05rem;
	line-height: 1.75;
	margin-bottom: 2rem;
}

.body-copy strong {
	color: var(--white);
	font-weight: 600;
}

blockquote {
	border-left: 2px solid var(--gold);
	padding-left: 1.2rem;
	font-size: 1.1rem;
	font-style: italic;
	color: var(--gold);
	line-height: 1.6;
}

.features {
	display: flex;
	flex-direction: column;
	gap: 0;
	padding-top: 4.5rem;
}

.features li {
	padding: 1rem 0;
	border-bottom: 1px solid var(--border);
	color: var(--white-dim);
	font-size: 0.95rem;
	display: flex;
	align-items: flex-start;
	gap: 0.8rem;
}

.features li::before {
	content: '—';
	color: var(--gold);
	flex-shrink: 0;
	margin-top: 0.02em;
}

.features li:first-child {
	border-top: 1px solid var(--border);
}


/* ────────────────────────── CONFIGS ────────────────────── */
.configs-section {
	padding: 8rem 0;
}

.text-center { text-align: center; }

.section-title {
	font-size: clamp(2rem, 4vw, 3rem);
	margin-bottom: 3.5rem;
}

.eyebrow.text-center { margin-bottom: 0.75rem; }

.configs-grid {
	display: grid;
	grid-template-columns: repeat(5, 1fr);
	gap: 1rem;
}

.badge-recommended {
	position: absolute;
	top: -0.65rem;
	right: 1rem;
	background: var(--gold);
	color: #0a0a0a;
	font-family: var(--font-mono);
	font-size: 0.58rem;
	font-weight: 700;
	letter-spacing: 0.12em;
	text-transform: uppercase;
	padding: 0.22rem 0.7rem;
	border-radius: 2rem;
	white-space: nowrap;
}

.config-card {
	background: rgba(255,255,255,0.025);
	border: 1px solid var(--border);
	border-radius: 1rem;
	padding: 2.2rem 1.6rem;
	transition: transform 0.3s ease, border-color 0.3s ease, background 0.3s ease;
	position: relative;
	display: flex;
	flex-direction: column;
	cursor: pointer;
	user-select: none;
}

.config-card::before {
	content: '';
	position: absolute;
	inset: 0;
	opacity: 0;
	transition: opacity 0.3s;
	border-radius: inherit;
}

.config-card:hover { transform: translateY(-6px); }

.config-master { border-color: rgba(232,168,58,0.2); }
.config-master::before { background: radial-gradient(circle at top left, rgba(232,168,58,0.07), transparent 60%); }
.config-master:hover { border-color: rgba(232,168,58,0.45); background: rgba(232,168,58,0.04); }
.config-master:hover::before { opacity: 1; }

.config-mid { border-color: rgba(74,143,231,0.2); }
.config-mid::before { background: radial-gradient(circle at top left, rgba(74,143,231,0.07), transparent 60%); }
.config-mid:hover { border-color: rgba(74,143,231,0.45); background: rgba(74,143,231,0.04); }
.config-mid:hover::before { opacity: 1; }

.config-high { border-color: rgba(160,100,240,0.2); }
.config-high::before { background: radial-gradient(circle at top left, rgba(160,100,240,0.07), transparent 60%); }
.config-high:hover { border-color: rgba(160,100,240,0.45); background: rgba(160,100,240,0.04); }
.config-high:hover::before { opacity: 1; }

.config-lower { border-color: rgba(80,200,180,0.2); }
.config-lower::before { background: radial-gradient(circle at top left, rgba(80,200,180,0.07), transparent 60%); }
.config-lower:hover { border-color: rgba(80,200,180,0.45); background: rgba(80,200,180,0.04); }
.config-lower:hover::before { opacity: 1; }

.config-toast { border-color: rgba(100,200,120,0.2); }
.config-toast::before { background: radial-gradient(circle at top left, rgba(100,200,120,0.07), transparent 60%); }
.config-toast:hover { border-color: rgba(100,200,120,0.45); background: rgba(100,200,120,0.04); }
.config-toast:hover::before { opacity: 1; }

.config-rank {
	font-family: var(--font-mono);
	font-size: 0.6rem;
	letter-spacing: 0.2em;
	color: var(--muted);
	margin-bottom: 1rem;
}

.config-icon-big {
	font-size: 2.2rem;
	margin-bottom: 0.8rem;
	display: block;
}

.config-card h3 {
	font-size: 1.3rem;
	margin-bottom: 0.7rem;
}

.config-card p {
	color: var(--white-dim);
	font-size: 0.9rem;
	line-height: 1.6;
	margin-bottom: 1.2rem;
}

.config-tags {
	display: flex;
	flex-wrap: wrap;
	gap: 0.4rem;
	margin-bottom: 1rem;
}

.config-tags span {
	font-family: var(--font-mono);
	font-size: 0.65rem;
	letter-spacing: 0.06em;
	padding: 0.25rem 0.65rem;
	border-radius: 2rem;
	border: 1px solid var(--border);
	color: var(--muted);
}

.card-copied-overlay {
	position: absolute;
	inset: 0;
	border-radius: inherit;
	background: rgba(8, 8, 18, 0.88);
	backdrop-filter: blur(4px);
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	gap: 0.3rem;
	animation: fadeUp 0.2s ease both;
}

.card-copied-check {
	font-size: 2rem;
	color: rgb(100, 220, 120);
	line-height: 1;
}

.card-copied-main {
	font-family: var(--font-head);
	font-size: 1rem;
	font-weight: 600;
	color: rgb(100, 220, 120);
	margin: 0;
}

.card-copied-hint {
	font-family: var(--font-mono);
	font-size: 0.65rem;
	letter-spacing: 0.06em;
	color: var(--muted);
	text-align: center;
	padding: 0 1rem;
	margin: 0;
}


/* ───────────────────────── PROFILES ────────────────────── */
.profiles-section {
	padding: 8rem 0;
	background: linear-gradient(to bottom, transparent, rgba(8,8,16,0.97), rgba(8,8,16,0.97), transparent);
}

.profiles-grid {
	display: grid;
	grid-template-columns: repeat(4, 1fr);
	gap: 1.2rem;
}

.profile-card {
	background: rgba(255,255,255,0.025);
	border: 1px solid var(--border);
	border-radius: 1rem;
	padding: 2rem 1.6rem;
	transition: transform 0.3s, border-color 0.3s, background 0.3s;
}

.profile-card:hover {
	transform: translateY(-5px);
	border-color: rgba(232,168,58,0.25);
	background: rgba(232,168,58,0.03);
}

.profile-icon {
	font-size: 2rem;
	display: block;
	margin-bottom: 1rem;
}

.profile-card h3 {
	font-size: 1.1rem;
	margin-bottom: 0.65rem;
}

.profile-card p {
	color: var(--white-dim);
	font-size: 0.88rem;
	line-height: 1.65;
}


/* ───────────────────────────── JOIN ────────────────────── */
.join-section {
	padding: 8rem 0;
}

.steps {
	display: flex;
	flex-direction: column;
	gap: 0;
	max-width: 760px;
	margin: 0 auto;
}

.step {
	display: grid;
	grid-template-columns: 80px 1fr;
	gap: 2rem;
	align-items: start;
	padding: 1rem;
	border-radius: 0.85rem;
	border: 1px solid transparent;
	transition: background 0.5s ease, border-color 0.5s ease, box-shadow 0.5s ease;
}

.step.step-highlight {
	background: rgba(232, 168, 58, 0.04);
	border-color: rgba(232, 168, 58, 0.3);
	box-shadow: 0 0 28px rgba(232, 168, 58, 0.07);
}

.step-num-wrap {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding-top: 0.1rem;
}

.step-num {
	font-family: var(--font-mono);
	font-size: 1.1rem;
	font-weight: 700;
	color: var(--gold);
	width: 3rem;
	height: 3rem;
	border: 1px solid var(--gold-dim);
	border-radius: 50%;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-shrink: 0;
}

.step-line {
	width: 1px;
	flex: 1;
	min-height: 3rem;
	background: linear-gradient(to bottom, var(--gold-dim), transparent);
	margin: 0.6rem 0;
}

.step-content {
	padding-bottom: 3rem;
}

.step-content h3 {
	font-size: 1.3rem;
	margin-bottom: 0.6rem;
}

.step-content p {
	color: var(--white-dim);
	font-size: 0.95rem;
	line-height: 1.7;
	margin-bottom: 1rem;
}

.step-warning {
	display: flex;
	align-items: flex-start;
	gap: 0.6rem;
	background: rgba(240, 100, 60, 0.08);
	border: 1px solid rgba(240, 100, 60, 0.3);
	border-radius: 0.5rem;
	padding: 0.75rem 1rem;
	font-size: 0.88rem;
	color: rgb(250, 140, 100);
	line-height: 1.5;
	margin-bottom: 1.1rem;
}

.step-warning svg {
	flex-shrink: 0;
	margin-top: 0.1rem;
	color: rgb(250, 140, 100);
}

.step-map-dl {
	display: inline-flex;
	align-items: center;
	gap: 0.5rem;
	font-family: var(--font-mono);
	font-size: 0.82rem;
	font-weight: 700;
	color: var(--white);
	background: rgba(255,255,255,0.06);
	border: 1px solid rgba(255,255,255,0.12);
	border-radius: 0.45rem;
	padding: 0.55rem 1.1rem;
	transition: background 0.2s, border-color 0.2s;
	margin-bottom: 1.2rem;
}

.step-map-dl:hover {
	background: rgba(255,255,255,0.1);
	border-color: rgba(255,255,255,0.22);
}

.map-size {
	font-size: 0.7rem;
	color: var(--muted);
	border: 1px solid var(--border);
	border-radius: 2rem;
	padding: 0.1rem 0.5rem;
	margin-left: 0.2rem;
}

.step-install-hint {
	font-size: 0.85rem;
	color: var(--muted);
	margin-bottom: 0.5rem !important;
}

.step-install-hint code {
	font-family: var(--font-mono);
	font-size: 0.82rem;
	color: var(--white-dim);
	background: rgba(255,255,255,0.05);
	padding: 0.1em 0.35em;
	border-radius: 0.2em;
}

.step-path {
	display: block;
	font-family: var(--font-mono);
	font-size: 0.76rem;
	color: var(--gold);
	background: rgba(232, 168, 58, 0.06);
	border: 1px solid var(--gold-dim);
	border-radius: 0.45rem;
	padding: 0.6rem 0.9rem;
	margin-bottom: 1rem;
	word-break: break-all;
	letter-spacing: 0.02em;
}

.step-link {
	font-family: var(--font-mono);
	font-size: 0.82rem;
	color: var(--gold);
	letter-spacing: 0.04em;
	transition: opacity 0.2s;
}

.step-link:hover { opacity: 0.7; }

.step-configs {
	display: flex;
	flex-wrap: wrap;
	gap: 0.6rem;
	margin-top: 0.5rem;
}

.config-dl {
	font-family: var(--font-mono);
	font-size: 0.75rem;
	padding: 0.4rem 1rem;
	border: 1px solid var(--border);
	border-radius: 0.35rem;
	color: var(--white-dim);
	background: transparent;
	cursor: pointer;
	transition: border-color 0.2s, color 0.2s, background 0.2s;
	display: inline-flex;
	align-items: center;
	gap: 0.3rem;
}

.config-dl:hover {
	border-color: var(--gold-dim);
	color: var(--gold);
}

.config-dl.copied {
	border-color: rgba(100, 220, 120, 0.5);
	color: rgb(100, 220, 120);
	background: rgba(100, 220, 120, 0.06);
}

.copy-icon {
	font-size: 0.8rem;
}


/* ───────────────────────── FINAL CTA ───────────────────── */
.cta-section {
	padding: 10rem 0;
	text-align: center;
	position: relative;
	overflow: hidden;
}

.cta-glow {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	width: 600px;
	height: 300px;
	background: radial-gradient(ellipse at center, rgba(88,101,242,0.12) 0%, transparent 70%);
	pointer-events: none;
}

.cta-inner {
	position: relative;
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 1.5rem;
}

.cta-hint {
	font-family: var(--font-mono);
	font-size: 0.78rem;
	color: var(--muted);
	letter-spacing: 0.04em;
}

.cta-hint code {
	color: var(--white-dim);
	background: rgba(255,255,255,0.06);
	padding: 0.1em 0.4em;
	border-radius: 0.2em;
	font-family: var(--font-mono);
}

.cta-title {
	font-size: clamp(2.5rem, 6vw, 5rem);
	margin: 0;
}


/* ─────────────────────── RESPONSIVE ────────────────────── */
@media (max-width: 900px) {
	.stats-grid {
		grid-template-columns: repeat(2, 1fr);
	}

	.concept-grid {
		grid-template-columns: 1fr;
	}

	.features {
		padding-top: 0;
	}

	.configs-grid {
		grid-template-columns: repeat(3, 1fr);
	}

	.profiles-grid {
		grid-template-columns: repeat(2, 1fr);
	}
}

@media (max-width: 600px) {
	.hero h1 { font-size: clamp(2.8rem, 14vw, 5rem); white-space: normal; }

	.stats-grid {
		grid-template-columns: 1fr 1fr;
	}

	.configs-grid {
		grid-template-columns: 1fr 1fr;
	}

	.profiles-grid {
		grid-template-columns: 1fr;
	}

	.step {
		grid-template-columns: 56px 1fr;
		gap: 1.2rem;
	}
}
</style>
