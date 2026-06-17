<script>
	import { onMount } from 'svelte';

	let canvas;

	class Particle {
		constructor(w, h, randomY = true) {
			this.w = w;
			this.h = h;
			this.x = Math.random() * w;
			this.y = randomY ? Math.random() * h : h + 5;
			this.size = 0.4 + Math.random() * 1.4;
			this.vy = 0.08 + Math.random() * 0.18;
			this.vx = (Math.random() - 0.5) * 0.08;
			this.alpha = 0.08 + Math.random() * 0.45;
			this.gold = Math.random() > 0.65;
		}

		update() {
			this.y -= this.vy;
			this.x += this.vx;
			if (this.y < -8) {
				this.y = this.h + 5;
				this.x = Math.random() * this.w;
			}
		}

		draw(ctx) {
			const r = this.gold ? '232,168,58' : '180,210,255';
			ctx.beginPath();
			ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
			ctx.fillStyle = `rgba(${r},${this.alpha})`;
			ctx.fill();
			ctx.beginPath();
			ctx.arc(this.x, this.y, this.size * 2.8, 0, Math.PI * 2);
			ctx.fillStyle = `rgba(${r},${this.alpha * 0.15})`;
			ctx.fill();
		}
	}

	onMount(() => {
		const ctx = canvas.getContext('2d');
		let w, h;
		let particles = [];
		let raf;
		const t0 = performance.now();

		function resize() {
			w = canvas.width = window.innerWidth;
			h = canvas.height = window.innerHeight;
			particles = Array.from({ length: 75 }, () => new Particle(w, h));
		}

		function drawBg() {
			const g = ctx.createRadialGradient(w * 0.5, h * 0.22, 0, w * 0.5, h * 0.22, Math.max(w, h));
			g.addColorStop(0,    '#12122a');
			g.addColorStop(0.38, '#0a0a18');
			g.addColorStop(1,    '#030308');
			ctx.fillStyle = g;
			ctx.fillRect(0, 0, w, h);
		}

		function drawRays(t) {
			const NUM = 14;
			const sx = w * 0.5;
			const sy = -h * 0.15;

			ctx.save();
			ctx.globalCompositeOperation = 'lighter';

			for (let i = 0; i < NUM; i++) {
				const spread  = Math.PI * 0.85;
				const base    = -Math.PI / 2 - spread / 2 + (i / (NUM - 1)) * spread;
				const wobble  = Math.sin(t * 0.00018 + i * 1.73) * 0.06;
				const angle   = base + wobble;
				const len     = Math.max(w, h) * 2.8;
				const ex      = sx + Math.cos(angle) * len;
				const ey      = sy + Math.sin(angle) * len;
				const half    = len * (0.026 + Math.sin(t * 0.0008 + i * 0.92) * 0.008);
				const nx      = Math.cos(angle + Math.PI / 2);
				const ny      = Math.sin(angle + Math.PI / 2);
				const pulse   = 0.022 + Math.sin(t * 0.00042 + i * 1.1) * 0.012;

				const g2 = ctx.createLinearGradient(sx, sy, ex, ey);
				g2.addColorStop(0,    `rgba(232,168,58,${pulse})`);
				g2.addColorStop(0.45, `rgba(200,140,60,${pulse * 0.5})`);
				g2.addColorStop(1,    'rgba(232,168,58,0)');

				ctx.beginPath();
				ctx.moveTo(sx - nx, sy - ny);
				ctx.lineTo(sx + nx, sy + ny);
				ctx.lineTo(ex + nx * half, ey + ny * half);
				ctx.lineTo(ex - nx * half, ey - ny * half);
				ctx.closePath();
				ctx.fillStyle = g2;
				ctx.fill();
			}

			ctx.restore();
		}

		function drawHorizon() {
			const g = ctx.createLinearGradient(0, 0, 0, h * 0.65);
			g.addColorStop(0,   'rgba(232,168,58,0.035)');
			g.addColorStop(0.5, 'rgba(70,100,200,0.02)');
			g.addColorStop(1,   'rgba(0,0,0,0)');
			ctx.fillStyle = g;
			ctx.fillRect(0, 0, w, h * 0.65);
		}

		function drawVignette() {
			const g = ctx.createRadialGradient(w/2, h/2, h * 0.28, w/2, h/2, Math.max(w, h) * 0.85);
			g.addColorStop(0, 'rgba(0,0,0,0)');
			g.addColorStop(1, 'rgba(0,0,0,0.72)');
			ctx.fillStyle = g;
			ctx.fillRect(0, 0, w, h);
		}

		function frame(now) {
			const t = now - t0;
			ctx.clearRect(0, 0, w, h);
			drawBg();
			drawRays(t);
			drawHorizon();
			for (const p of particles) { p.update(); p.draw(ctx); }
			drawVignette();
			raf = requestAnimationFrame(frame);
		}

		resize();
		window.addEventListener('resize', resize);
		raf = requestAnimationFrame(frame);

		return () => {
			cancelAnimationFrame(raf);
			window.removeEventListener('resize', resize);
		};
	});
</script>

<canvas bind:this={canvas}></canvas>

<style>
	canvas {
		position: fixed;
		inset: 0;
		width: 100%;
		height: 100%;
		z-index: 0;
		pointer-events: none;
	}
</style>
