<script setup>
import { ref } from 'vue';
import BuildingButton from '../components/BuildingButton.vue';
import Hearts from '../components/Hearts.vue'

let cookies = ref(0);
let cps = ref(0);

setInterval(() => {
    cookies.value += cps.value;
}, 1000);

let buildings = ref([
    {name: 'Milk', icon: '🥛', cps: 0.1, price: 15, count: 0},
    {name: 'Rose', icon: '🌹', cps: 1, price: 100, count: 0},
    {name: 'Cupid', icon: '💘', cps: 10, price: 1000, count: 0},
]);

// Heart burst: every 10th click on the cookie pops out a few hearts
let clicks = 0;
let burstId = 0;
let bursts = ref([]);
const burstSymbols = ['💗', '💖', '💕', '💘'];

// Cookie shake: restarts on every click, even rapid ones
let cookieImg = ref(null);

function shakeCookie() {
    if (!cookieImg.value) return;
    if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;
    cookieImg.value.animate(
        [
            { transform: 'translate(0, 0) rotate(0deg)' },
            { transform: 'translate(-4px, 1px) rotate(-3deg)' },
            { transform: 'translate(4px, -1px) rotate(3deg)' },
            { transform: 'translate(-3px, 1px) rotate(-2deg)' },
            { transform: 'translate(2px, 0) rotate(1deg)' },
            { transform: 'translate(0, 0) rotate(0deg)' },
        ],
        { duration: 220, easing: 'ease-out' }
    );
}

function clickCookie() {
    cookies.value++;
    shakeCookie();
    clicks++;
    if (clicks % 10 === 0) {
        spawnBurst();
    }
}

function spawnBurst() {
    const count = 6;
    for (let i = 0; i < count; i++) {
        const angle = (Math.PI * 2 * i) / count + Math.random() * 0.6;
        const distance = 90 + Math.random() * 60;
        const heart = {
            id: burstId++,
            symbol: burstSymbols[Math.floor(Math.random() * burstSymbols.length)],
            dx: Math.cos(angle) * distance,
            dy: Math.sin(angle) * distance - 30, // slight upward lean
            size: 22 + Math.random() * 14,
        };
        bursts.value.push(heart);
        setTimeout(() => {
            bursts.value = bursts.value.filter(h => h.id !== heart.id);
        }, 900);
    }
}

function buyBuilding(building) {
    if(cookies.value >= building.price) {
        cookies.value -= building.price;
        cps.value += building.cps;
        building.count++;
    }
}


</script>

<template>
    <div class="columns">
        <div class="column valentine-left has-text-centered">
            <h1>{{ parseFloat(cookies.toFixed(1)) }} cookies! 💕</h1>
            <h3>per second {{ parseFloat(cps.toFixed(1)) }}</h3>
            <div class="cookie-wrap" @click="clickCookie">
                <img
                    ref="cookieImg"
                    src="https://png.pngtree.com/png-vector/20250425/ourmid/pngtree-chocolate-chip-cookie-icon-white-background-png-image_16100260.png">
                <span
                    v-for="heart in bursts"
                    :key="heart.id"
                    class="burst-heart"
                    :style="{
                        '--dx': heart.dx + 'px',
                        '--dy': heart.dy + 'px',
                        fontSize: heart.size + 'px',
                    }"
                >{{ heart.symbol }}</span>
            </div>
        </div>
        <div class="column valentine-middle has-text-centered">
          <Hearts />
          <div class="garland">
            <span v-for="n in 15" :key="n" class="garland-heart">💗</span>
          </div>
        </div>

        <div class="column valentine-right">
           <BuildingButton v-for="building in buildings" :building="building" :disabled="cookies<building.price" @click="buyBuilding(building)"></BuildingButton>
        </div>
    </div>
</template>

<style scoped>
/* Valentine palette */
.valentine-left {
    background-color: #8c1c3d;   /* deep rose */
    color: #fff0f3;
}

.valentine-middle {
    position: relative;          /* keeps the floating hearts inside this column */
    overflow: hidden;
    background-color: #ffc2d1;   /* blush pink */
}

.valentine-right {
    background-color: #590d22;   /* dark burgundy */
    color: #fff0f3;
}

.valentine-left h1,
.valentine-left h3 {
    color: #fff0f3;
}

/* Hearts that pop out of the cookie every 10th click */
.cookie-wrap {
    position: relative;
}

.burst-heart {
    position: absolute;
    top: 50%;
    left: 50%;
    pointer-events: none;
    user-select: none;
    animation: pop 0.9s ease-out forwards;
}

@keyframes pop {
    0% {
        transform: translate(-50%, -50%) scale(0.4);
        opacity: 1;
    }
    100% {
        transform: translate(calc(-50% + var(--dx)), calc(-50% + var(--dy))) scale(1.2);
        opacity: 0;
    }
}

/* Row of hearts along the top, replaces the Christmas lights */
.garland {
    display: flex;
    justify-content: space-around;
    position: relative;
    z-index: 1;
}

.garland-heart {
    font-size: 22px;
    animation: pulse 1.6s ease-in-out infinite;
}

.garland-heart:nth-child(even) {
    animation-delay: 0.8s;
}

@keyframes pulse {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(1.25); }
}

@media (prefers-reduced-motion: reduce) {
    .garland-heart {
        animation: none;
    }
}
</style>