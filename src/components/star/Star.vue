<script lang="ts" setup>import { onMounted, ref } from "@vue/runtime-core";
let sky = ref<any>()
let canvas = ref<HTMLCanvasElement>()


onMounted(() => {
    sky.value.height = window.innerHeight
    sky.value.width = window.innerWidth

    let ctx = canvas.value?.getContext("2d")
    let _width = sky.value.width
    let _height = sky.value.height
    let stars: (any)[] = []
    let initStarsPopulation = 80;
    class Star {
        private id: any;
        private x: any;
        private y: any;
        private r: any;
        private alpha: any = (Math.floor(Math.random() * 10) + 1) / 10 / 2
        private color: any;
        constructor(id: any, x: any, y: any) {
            this.id = id;
            this.x = x;
            this.y = y
            this.r = Math.floor(Math.random() * 2) + 1
            this.color = `rgba(255,255,255,${this.alpha})`
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
        move() {
            this.y -= 0.2
            if (this.y <= -10) this.y = _height + 10
            this.draw()
        }
        die() {
            stars[this.id] = null;
            delete stars[this.id]
        }
    }
    const animate = () => {
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
    const setCanvasSise = () => {
        canvas.value?.setAttribute("width", _width)
        canvas.value?.setAttribute("height", _height)
    }
    setCanvasSise()
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