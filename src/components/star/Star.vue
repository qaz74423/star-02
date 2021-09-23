<script lang="ts" setup>import { onMounted, ref } from "@vue/runtime-core";
let sky = ref<HTMLDivElement>()
let canvas = ref<HTMLCanvasElement>()
let _width: number
let _height: number
let stars: (Star)[] = []

const initStarsPopulation = 80;
let ctx: (CanvasRenderingContext2D | null | undefined)
class Star {
    private id: number;
    private x: number;
    private y: number;
    private r: number = Math.floor(Math.random() * 2) + 1;
    private alpha: number = (Math.floor(Math.random() * 10) + 1) / 10 / 2
    private color: string = `rgba(255,255,255,${this.alpha})`

    constructor(id: number, x: number, y: number) {
        this.id = id;
        this.x = x;
        this.y = y
    }

    draw() {
        if (!ctx) return
        ctx.fillStyle = this.color;//填充色
        ctx.shadowBlur = this.r * 2//阴影
        ctx.beginPath();//起始路径
        ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false)//弧
        ctx.closePath();//闭合路径
        ctx.fill()//填充
    }
    move(startHeight: number = _height, speed: number = 1) {
        this.y -= speed
        if (this.y <= -10) this.y = startHeight
        this.draw()
    }
    // die() {
    //     stars[this.id] = null;
    //     delete stars[this.id]
    // }
}
const animate = (): void => {
    ctx?.clearRect(0, 0, _width, _height);
    for (let i in stars) {
        stars[i].move();
    }
    requestAnimationFrame(animate)
}
const __init__ = () => {
    if (!ctx) return
    ctx.strokeStyle = "white";
    ctx.shadowColor = "white"

    for (let i = 0; i < initStarsPopulation; i++) {
        stars[i] = new Star(i, Math.floor(Math.random() * _width), Math.floor(Math.random() * _height))
    }
    ctx.shadowBlur = 0;
    animate();
}

const setCanvasSise = (w: number, h: number): void => {
    canvas.value?.setAttribute("width", w + "")
    canvas.value?.setAttribute("height", h + "")
}
onMounted(() => {
    _height = window.innerHeight
    _width = window.innerWidth
    if (sky.value) {
        sky.value.style.height = _height + "px"
        sky.value.style.width = _width + "px"
    }

    ctx = canvas.value?.getContext("2d")

    setCanvasSise(_width, _height)
    __init__()

})
</script>

<template>
    <div class="sky" ref="sky">
        <canvas ref="canvas"></canvas>
        <!-- <div class="bg-img"></div> -->
        <div class="filter"></div>
    </div>
</template>

<style scoped lang="scss">
@keyframes colorChange {
    0%,
    100% {
        opacity: 0;
    }
    50% {
        opacity: 0.9;
    }
}
.sky {
    background: radial-gradient(
        65% 105% at bottom center,
        #f7f7b6 10%,
        #e96f92 40%,
        #75517d 65%,
        #1b2947
    );
    .filter {
        position: fixed;
        width: 100%;
        height: 100%;
        top: 0;
        background-color: #f00000;
        mix-blend-mode: overlay;
        animation: colorChange 30s ease-in-out infinite;
        animation-fill-mode: both;
    }
}
</style>