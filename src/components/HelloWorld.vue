<template>
  <svg id="halftone-bg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 800" preserveAspectRatio="xMidYMid slice">
    <defs>
        <radialGradient id="color-gradient">
            <!-- Will be updated dynamically -->
        </radialGradient>
    </defs>
    
    <rect width="1200" height="800" fill="#1a1a1a"/>
    <rect id="gradient-rect" width="1200" height="800" fill="url(#color-gradient)" opacity="0.6"/>
    <g id="dots-layer">
        <!-- Dots will be generated here -->
    </g>
  </svg>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted() {
    const svg = document.getElementById('halftone-bg');
    const dotsLayer = document.getElementById('dots-layer');
    const gradientRect = document.getElementById('gradient-rect');
    const colorGradient = document.getElementById('color-gradient');
    
    const colors = ['#FFFF00', '#00FF00', '#00FFFF', '#0040FF', '#FF0080'];
    
    // Generate organic halftone pattern
    function generateHalftone() {
        // Clear existing dots
        dotsLayer.innerHTML = '';
        
        // Create random color centers for gradient
        const numCenters = 3 + Math.floor(Math.random() * 3); // 3-5 centers
        const centers = [];
        
        for (let i = 0; i < numCenters; i++) {
            centers.push({
                x: Math.random(),
                y: Math.random(),
                color: colors[Math.floor(Math.random() * colors.length)]
            });
        }
        
        // Update gradient with new centers
        updateGradient(centers);
        
        // Generate dots with organic variation
        const spacing = 10;
        const jitter = 3; // Random position offset
        
        for (let y = 0; y < 800; y += spacing) {
            for (let x = 0; x < 1200; x += spacing) {
                // Add organic offset
                const offsetX = x + (Math.random() - 0.5) * jitter;
                const offsetY = y + (Math.random() - 0.5) * jitter;
                
                // Calculate dot size based on distance from color centers
                const nx = x / 1200;
                const ny = y / 800;
                
                let intensity = 0;
                centers.forEach(center => {
                    const dx = nx - center.x;
                    const dy = ny - center.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    intensity += Math.exp(-distance * 3);
                });
                
                // Normalize and add noise
                intensity = intensity / numCenters;
                intensity += (Math.random() - 0.5) * 0.3;
                intensity = Math.max(0, Math.min(1, intensity));
                
                // Vary dot size based on intensity
                const minSize = 1;
                const maxSize = 4;
                const dotSize = minSize + (maxSize - minSize) * (1 - intensity);
                
                // Create dot
                const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                circle.setAttribute('cx', offsetX);
                circle.setAttribute('cy', offsetY);
                circle.setAttribute('r', dotSize);
                circle.setAttribute('fill', '#000000');
                circle.setAttribute('opacity', 0.15 + intensity * 0.15);
                
                dotsLayer.appendChild(circle);
            }
        }
    }
    
    // Update the radial gradient
    function updateGradient(centers) {
        colorGradient.innerHTML = '';
        
        // Pick a main center
        const mainCenter = centers[0];
        colorGradient.setAttribute('cx', `${mainCenter.x * 100}%`);
        colorGradient.setAttribute('cy', `${mainCenter.y * 100}%`);
        
        // Create gradient stops
        centers.forEach((center, i) => {
            const stop = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
            const offset = (i / (centers.length - 1)) * 100;
            stop.setAttribute('offset', `${offset}%`);
            stop.setAttribute('stop-color', center.color);
            colorGradient.appendChild(stop);
        });
    }
    
    // Transition to new pattern
    function transitionPattern() {
        svg.classList.add('transitioning');
        
        setTimeout(() => {
            generateHalftone();
            svg.classList.remove('transitioning');
        }, 2000);
    }
    
    // Initial generation
    generateHalftone();
    
    // Change pattern every 30 seconds
    setInterval(transitionPattern, 30000);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#halftone-bg {
  position: fixed;
  inset: 0;
  z-index: -1;
  transition: opacity 2s ease-in-out;
}

#halftone-bg.transitioning {
  opacity: 0;
}
</style>
