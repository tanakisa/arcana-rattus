<script>
  import { onMount } from "svelte";

  let canvas;

  onMount(() => {
    const context = canvas.getContext("2d");
    if (!context) return;

    const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)");
    const ratLines = [
      [0, 1], [1, 2], [2, 3], [3, 4], [4, 5], [5, 6], [6, 7], [7, 8],
      [8, 9], [9, 10], [10, 11], [11, 12], [12, 0], [0, 13], [13, 14],
      [14, 15], [15, 16], [16, 17], [17, 18], [18, 19], [19, 20], [20, 21],
      [21, 22], [22, 23], [23, 24], [24, 6], [5, 25], [25, 26], [26, 27],
      [27, 28], [28, 29], [29, 30], [30, 3], [4, 31], [31, 32]
    ];
    const ratPoints = [
      [.32, .53], [.38, .44], [.48, .4], [.59, .41], [.68, .45], [.75, .47], [.81, .44],
      [.85, .4], [.89, .42], [.91, .48], [.97, .51], [.91, .55], [.88, .6], [.78, .63],
      [.68, .67], [.55, .68], [.44, .65], [.35, .61], [.25, .58], [.19, .52], [.15, .46],
      [.22, .49], [.28, .54], [.35, .56], [.42, .55], [.82, .35], [.85, .3], [.9, .31],
      [.92, .37], [.89, .42], [.85, .43], [.84, .52], [.82, .51]
    ];

    let width = 0;
    let height = 0;
    let stars = [];
    let constellation = null;
    let animationFrame = 0;
    let lastFrame = 0;
    let nextConstellation = 0;

    function resizeCanvas() {
      const ratio = Math.min(window.devicePixelRatio || 1, 2);
      width = window.innerWidth;
      height = window.innerHeight;
      canvas.width = Math.round(width * ratio);
      canvas.height = Math.round(height * ratio);
      context.setTransform(ratio, 0, 0, ratio, 0, 0);
      const count = Math.min(105, Math.max(42, Math.round(width * height / 12500)));
      stars = Array.from({ length: count }, () => ({
        x: Math.random() * width,
        y: Math.random() * height,
        radius: .4 + Math.random() * 1.1,
        alpha: .12 + Math.random() * .38,
        speed: .08 + Math.random() * .24,
        phase: Math.random() * Math.PI * 2
      }));
    }

    function startConstellation(time) {
      if (width < 580 || reducedMotion.matches) return;
      const scale = Math.min(width * .33, height * .32, 240);
      constellation = {
        x: Math.random() * Math.max(1, width - scale),
        y: Math.random() * Math.max(1, height - scale),
        scale,
        started: time,
        duration: 6500 + Math.random() * 4500
      };
      nextConstellation = time + 14000 + Math.random() * 13000;
    }

    function drawConstellation(time) {
      if (!constellation && width >= 580 && time >= nextConstellation) startConstellation(time);
      if (!constellation) return;

      const elapsed = time - constellation.started;
      if (elapsed >= constellation.duration) {
        constellation = null;
        return;
      }

      const fade = Math.min(1, elapsed / 1400, (constellation.duration - elapsed) / 1600);
      const { x, y, scale } = constellation;
      context.save();
      context.lineWidth = 1;
      context.strokeStyle = `rgba(229, 199, 121, ${.13 * Math.max(0, fade)})`;
      context.beginPath();
      for (const [from, to] of ratLines) {
        const a = ratPoints[from];
        const b = ratPoints[to];
        context.moveTo(x + a[0] * scale, y + a[1] * scale);
        context.lineTo(x + b[0] * scale, y + b[1] * scale);
      }
      context.stroke();
      context.fillStyle = `rgba(255, 228, 157, ${.42 * Math.max(0, fade)})`;
      for (const [pointX, pointY] of ratPoints) {
        context.beginPath();
        context.arc(x + pointX * scale, y + pointY * scale, 1.25, 0, Math.PI * 2);
        context.fill();
      }
      context.restore();
    }

    function drawSky(time) {
      const elapsed = Math.min(50, time - (lastFrame || time));
      lastFrame = time;
      context.clearRect(0, 0, width, height);
      for (const star of stars) {
        star.y += star.speed * elapsed / 16;
        star.x += Math.sin(time * .00015 + star.phase) * .035;
        if (star.y > height + 3) {
          star.y = -3;
          star.x = Math.random() * width;
        }
        const pulse = .78 + .22 * Math.sin(time * .001 + star.phase);
        context.beginPath();
        context.fillStyle = `rgba(238, 211, 139, ${star.alpha * pulse})`;
        context.shadowColor = "rgba(238, 211, 139, .55)";
        context.shadowBlur = star.radius > 1 ? 7 : 3;
        context.arc(star.x, star.y, star.radius, 0, Math.PI * 2);
        context.fill();
      }
      context.shadowBlur = 0;
      drawConstellation(time);
      if (!reducedMotion.matches) animationFrame = window.requestAnimationFrame(drawSky);
    }

    function handleMotionPreferenceChange() {
      window.cancelAnimationFrame(animationFrame);
      constellation = null;
      lastFrame = 0;
      drawSky(performance.now());
    }

    resizeCanvas();
    window.addEventListener("resize", resizeCanvas, { passive: true });
    reducedMotion.addEventListener("change", handleMotionPreferenceChange);
    drawSky(performance.now());

    return () => {
      window.cancelAnimationFrame(animationFrame);
      window.removeEventListener("resize", resizeCanvas);
      reducedMotion.removeEventListener("change", handleMotionPreferenceChange);
    };
  });
</script>

<canvas bind:this={canvas} id="sky" aria-hidden="true"></canvas>
