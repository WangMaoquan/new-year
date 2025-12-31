<script setup lang="ts">
  import { ref, onMounted, onUnmounted } from 'vue';

  interface Particle {
    x: number;
    y: number;
    vx: number;
    vy: number;
    size: number;
    color: string;
    alpha: number;
    decay: number;
    gravity: number;
  }

  interface Firework {
    x: number;
    y: number;
    particles: Particle[];
  }

  const canvas = ref<HTMLCanvasElement | null>(null);
  let ctx: CanvasRenderingContext2D | null = null;
  let animationId: number | null = null;
  const fireworks = ref<Firework[]>([]);

  const colors = [
    '#ff6b6b',
    '#ffd93d',
    '#6bcf7f',
    '#4d96ff',
    '#ff6bff',
    '#ffa500',
    '#ff69b4',
    '#00ffff',
    '#7fff00',
    '#ff1493',
  ];

  const createFirework = (x: number, y: number) => {
    const particles: Particle[] = [];
    const particleCount = 80 + Math.random() * 70; // Reduced from 200-400 to ~100-150 for performance

    for (let i = 0; i < particleCount; i++) {
      const angle = (Math.PI * 2 * i) / particleCount;
      const velocity = 2 + Math.random() * 5; // Slightly reduced velocity
      const color = colors[Math.floor(Math.random() * colors.length)]!;

      particles.push({
        x,
        y,
        vx: Math.cos(angle) * velocity,
        vy: Math.sin(angle) * velocity,
        size: 2 + Math.random() * 3, // Slightly smaller range
        color,
        alpha: 1,
        decay: 0.01 + Math.random() * 0.015, // Faster decay to clear particles sooner
        gravity: 0.05 + Math.random() * 0.05,
      });
    }

    return { x, y, particles };
  };

  const updateFireworks = () => {
    fireworks.value = fireworks.value.filter((firework) => {
      firework.particles = firework.particles.filter((particle) => {
        particle.x += particle.vx;
        particle.y += particle.vy;
        particle.vy += particle.gravity;
        particle.alpha -= particle.decay;
        particle.vx *= 0.98;
        particle.vy *= 0.98;

        return particle.alpha > 0;
      });

      return firework.particles.length > 0;
    });
  };

  const drawFireworks = () => {
    if (!ctx || !canvas.value) return;

    // Use destination-out to fade existing canvas content to transparent
    // This allows the CSS background of the parent component to show through
    ctx.globalCompositeOperation = 'destination-out';
    ctx.fillStyle = 'rgba(0, 0, 0, 0.1)'; // Slower fade for better trails
    ctx.fillRect(0, 0, canvas.value.width, canvas.value.height);

    // Reset composite operation to draw new particles
    ctx.globalCompositeOperation = 'lighter';

    fireworks.value.forEach((firework) => {
      firework.particles.forEach((particle) => {
        if (!ctx) return;

        ctx.save();
        ctx.globalAlpha = particle.alpha;
        ctx.fillStyle = particle.color;
        ctx.beginPath();
        ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
        ctx.fill();

        // Removed heavy shadowBlur for performance.
        // 'lighter' composite mode already provides sufficient glow.

        // Add white core for brightness (simplified)
        ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
        ctx.beginPath();
        ctx.arc(particle.x, particle.y, particle.size * 0.5, 0, Math.PI * 2);
        ctx.fill();

        ctx.restore();
      });
    });

    // Reset to default for next frame (though we set it at start of drawFireworks)
    ctx.globalCompositeOperation = 'source-over';
  };

  const animate = () => {
    updateFireworks();
    drawFireworks();
    animationId = requestAnimationFrame(animate);
  };

  const launchRandomFirework = () => {
    if (!canvas.value) return;

    const x = Math.random() * canvas.value.width;
    const y = Math.random() * (canvas.value.height * 0.6);

    fireworks.value.push(createFirework(x, y));
  };

  const resizeCanvas = () => {
    if (!canvas.value) return;

    canvas.value.width = window.innerWidth;
    canvas.value.height = window.innerHeight;
  };

  onMounted(() => {
    if (!canvas.value) return;

    ctx = canvas.value.getContext('2d');
    resizeCanvas();
    animate();

    // Launch fireworks periodically
    const interval = setInterval(() => {
      launchRandomFirework();
    }, 400);

    window.addEventListener('resize', resizeCanvas);

    onUnmounted(() => {
      if (animationId) cancelAnimationFrame(animationId);
      clearInterval(interval);
      window.removeEventListener('resize', resizeCanvas);
    });
  });
</script>

<template>
  <canvas
    ref="canvas"
    class="fixed top-0 left-0 w-full h-full pointer-events-none z-1"
  ></canvas>
</template>
