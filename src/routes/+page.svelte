<script>
	import { onMount } from 'svelte';

	const DISCORD = 'https://discord.gg/VOTRE_LIEN_ICI';

	const stats = [
		{ value: 2064,  suffix: '',  label: 'chunks\nrender distance', sub: 'vs. 10 sur un Realm standard' },
		{ value: 160,   suffix: '',  label: 'fps\ngarantis', sub: 'shaders fine-tunés à la main' },
		{ value: 64000, suffix: '',  label: 'blocs\nde map',  sub: 'pré-générée, world border inclus' },
		{ value: 250,   suffix: '+', label: 'mods\nactifs',   sub: 'invisibles — ils perfectionnent' },
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
			desc: 'Tu veux explorer, progresser, dominer. Une map de 64 000 blocs pré-générée t\'attend. L\'End s\'ouvre après deux semaines — sois prêt.',
		},
		{
			icon: '🤝',
			title: 'Compagnon',
			desc: 'Le social avant tout. Des waystones pour se retrouver, des mini-events réguliers, et des coffres à sauter à 2h du mat en vocal sur Discord.',
		},
		{
			icon: '🌱',
			title: 'Débutant',
			desc: 'Premier serveur ? La config Grille-Pain tourne sur un vieux laptop qui ne supportait même pas le vanilla de base. Bienvenue.',
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
			body: 'Clique sur Add Instance → Import → colle l\'URL de ta config. 3 versions selon ton PC :',
			configs: [
				{ label: 'Master Race',  href: '#' },
				{ label: 'Mid-range',    href: '#' },
				{ label: 'Grille-Pain', href: '#' },
			],
		},
		{
			num: '03',
			title: 'Se connecter',
			body: 'Ajoute ton pseudo dans #whitelist✅ sur Discord, lance l\'instance. TILTEDCRAFT apparaît automatiquement dans Multijoueur.',
		},
	];

	let statsEl;
	let animated = false;

	// Countdown — ouverture le 22/06/2026 à 18h00
	const LAUNCH = new Date('2026-06-22T18:00:00+02:00');
	let cd = { d: 0, h: 0, m: 0, s: 0, launched: false };

	function tick() {
		const diff = LAUNCH - Date.now();
		if (diff <= 0) { cd = { d: 0, h: 0, m: 0, s: 0, launched: true }; return; }
		cd = {
			d: Math.floor(diff / 86400000),
			h: Math.floor((diff % 86400000) / 3600000),
			m: Math.floor((diff % 3600000) / 60000),
			s: Math.floor((diff % 60000) / 1000),
			launched: false,
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
	<meta name="description" content="Serveur Minecraft Vanilla++ — ~250 mods, shaders custom, 160fps, 2064 chunks de render distance." />
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
		{#if !cd.launched}
		<div class="countdown">
			<p class="cd-label">Ouverture du serveur dans</p>
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
			<p class="cd-date">22 · 06 · 2026 — 18h00</p>
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
					Un serveur 1.20.1 Fabric tournant avec ~250 mods qui ne changent pas le jeu — ils le <strong>perfectionnent</strong>. L'unique mod véritablement visible ? <strong>Create.</strong> Tout le reste travaille dans l'ombre&nbsp;: rendu, performances, qualité de vie.
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
		<h2 class="section-title text-center reveal">3 configs, zéro excuse</h2>

		<div class="configs-grid">
			<div class="config-card config-master reveal" style="animation-delay:0ms">
				<div class="config-rank">PC MASTER RACE</div>
				<div class="config-icon-big">🚀</div>
				<h3>Master Race</h3>
				<p>160 fps en full shaders. Render distance maximale. L'expérience comme elle a été pensée.</p>
				<div class="config-tags">
					<span>160 fps</span>
					<span>Full shaders</span>
					<span>Ultra render</span>
				</div>
			</div>

			<div class="config-card config-mid reveal" style="animation-delay:80ms">
				<div class="config-rank">MID-RANGE</div>
				<div class="config-icon-big">⚡</div>
				<h3>Mid-range</h3>
				<p>Performances équilibrées, shaders optimisés. Jouer bien, sans sacrifier le visuel.</p>
				<div class="config-tags">
					<span>Équilibré</span>
					<span>Shaders opt.</span>
				</div>
			</div>

			<div class="config-card config-toast reveal" style="animation-delay:160ms">
				<div class="config-rank">GRILLE-PAIN</div>
				<div class="config-icon-big">🍞</div>
				<h3>Grille-Pain</h3>
				<p>Tourne sur un vieux laptop qui ne supportait même pas le vanilla de base. Vraiment.</p>
				<div class="config-tags">
					<span>Ultra léger</span>
					<span>Vieux PC ✓</span>
				</div>
			</div>
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
				<div class="step reveal" style="animation-delay:{i * 100}ms">
					<div class="step-num-wrap">
						<span class="step-num">{s.num}</span>
						{#if i < steps.length - 1}
							<div class="step-line"></div>
						{/if}
					</div>
					<div class="step-content">
						<h3>{s.title}</h3>
						<p>{s.body}</p>
						{#if s.action}
							<a href={s.action.href} target="_blank" rel="noopener noreferrer" class="step-link">{s.action.label}</a>
						{/if}
						{#if s.configs}
							<div class="step-configs">
								{#each s.configs as cfg}
									<a href={cfg.href} class="config-dl">{cfg.label}</a>
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
	grid-template-columns: repeat(3, 1fr);
	gap: 1.5rem;
}

.config-card {
	background: rgba(255,255,255,0.025);
	border: 1px solid var(--border);
	border-radius: 1rem;
	padding: 2.2rem;
	transition: transform 0.3s ease, border-color 0.3s ease, background 0.3s ease;
	position: relative;
	overflow: hidden;
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
	transition: border-color 0.2s, color 0.2s;
}

.config-dl:hover {
	border-color: var(--gold-dim);
	color: var(--gold);
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
		grid-template-columns: 1fr;
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

	.profiles-grid {
		grid-template-columns: 1fr;
	}

	.step {
		grid-template-columns: 56px 1fr;
		gap: 1.2rem;
	}
}
</style>
