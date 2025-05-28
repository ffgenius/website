---
title: CSS手残党救星：motion-v 助你写出流畅丝滑的动画
date: '2025-04-16T09:00:00.000+00:00'
lang: zh
duration: 17min
---
[[toc]]

## 前言

> React： Vue 就是逊啦，生态这么拉跨，哪能比得上我们高贵的 React 生态呢。
>
> Vue：无语，和你说不下去，典型的 React 思维。都 5202 年了，谁还在造谣我们 Vue 的生态差。

不知不觉间，`Vue 3` 自发布至今已悄然走过近五个年头。在这五年里，`Vue` 社区始终不懈努力，持续拓展并壮大着 `Vue` 的生态系统，秉持着“`React` 有的，`Vue` 也要有”的劲头，展现出一种“不蒸馒头争口气”的拼搏精神。

前些日子，尤大发布了一条推文，大致内容是：在 React 生态中那些备受用户赞誉的库，如今在 `Vue` 领域也都有了对应的替代方案。或许这些替代品在成熟度上尚无法与 React 的同类产品相媲美，但它们一直在不断进步、日益完善！

![image-20250408201258917](/images/motion-v-x.jpg)

在 React 生态中，`Framer Motion` 一直深受开发者青睐，它极大地简化了动画操作流程，且性能表现极为出色。而在 `Vue` 生态中，尽管市面上已有一些动画库（如 `vueuse/motion`）可供选择，但仍难以完全满足开发者的需求。此外，`GSAP` 也是不少开发者会考虑的方案，它基于原生写法实现动画效果，但操作流程较为繁琐，且仅有少数功能可免费使用。

[`motnon-vue`](https://github.com/motiondivision/motion-vue) 基于 `Framer Motion` 实现的一套 `Vue` 动画库，目前已收获 1.7k star，该仓库目前被收录到 [`motiondivision`](https://github.com/motiondivision) 组织下，作者也收到了来自尤大的 `sponsor`。

该动画库潜力无限，趁着这波热度，咱们也来蹭一蹭流量，这次就来一个 `motion-vue` 的使用教程。**注意：文章篇幅过长，你可直接滑动到文末查看省流。**

![video1](/images/motion-v-video1.gif)
![video1](/images/motion-v-video2.gif)

## 使用

```bash
# 安装
npm install motion-v
```

使用标签的形式引用

```vue
<template>
  <motion.div />
</template>
```

## 动画

### 基础用法

```vue
<script setup>
// 引入依赖
import { motion } from 'motion-v'
</script>

<template>
  // 使用标签的形式，创建一个 div 标签
  // initial 设置 div 的初始位置
  // animate 设置 div 的运动轨迹
  <motion.div
      class="box"
      :style="{ x: '-100px' }"
      :animate="{ x: '100px' }"
  />
</template>

<style scoped>
.box {
  width: 100px;
  height: 100px;
  background-color: mediumspringgreen;
}
</style>
```

当`motion` 组件初始化挂载后，动画立即执行，`div` 盒子从 `transform: translateX(-100px);` 移动至 `transform: translateX(100px);` 的位置。这样一个简单的动画效果就实现了，是不是非常的 `easy`。

- `motion` 可以添加任何的 `css` 效果作为动画效果，就好像我们在写 `css` 动画一般
- `motion` 支持数字、字符串以及颜色的值，从而实现一个动画效果
- 也支持 `display` 或 `visibility` 从可见到不可见的动画效果
- 同样的 `transfrom` 变化也是预料之中，不同的是，`motion` 可以单独设置每个变化轴的动画效果
  - Translate： `x`, `y`, `z`
  - Scale： `scale`, `scaleX`, `scaleY`
  - Rotate： `rotate`, `rotateX`, `rotateY`, `rotateZ`
  - Skew：`skew`, `skewX`, `skewY`
  - Perspective: `transformPerspective`
- 针对于 `SVG` 元素，同样也能设置动画

我们也能在动画中使用 `css` 变量，例如：

```vue
<motion.div
  class="box"
  :initial="{ '--x': '0px' }"
  :animate="{ '--x': '100px' }"
  :transition="{ duration: 2, repeat: Infinity }"
  :style="{ transform: 'translateX(var(--x))' }"
/>
```

直接使用 `css` 变量会照成性能问题，建议使用 `MotionValue` 来实现，这个我们在后续会有介绍。

**进入动画**

当设置 `initial` 初始值后，`motion` 组件挂载后，值将会从 `initial` 过渡至 `animate`。同时，`initial`也支持布尔值，设置为 `false` 将紧张进入动画效果。

**退出动画**

```vue
<script setup>
import { motion, AnimatePresence } from 'motion-v'
import { ref } from 'vue'

const isVisible = ref(true)
</script>

<template>
  <AnimatePresence>
    <motion.div
        v-if="isVisible"
        class="box"
        :initial="{ opacity: 0 }"
        :animate="{ opacity: 1 }"
        :exit="{ opacity: 0 }"
    />
  </AnimatePresence>
  <button @click="isVisible = !isVisible">toggle</button>
</template>

<style scoped>
.box {
  width: 100px;
  height: 100px;
  background-color: mediumspringgreen;
}
</style>
```

由于当 `isVisible` 为 `false` 时，`motion` 组件马上被销毁，我们无法正确执行退出动画，因此我们需要使用 `AnimatePresence`，当组件执行 `exit`，暂时将元素保留，直至动画结束。

**关键帧**

在 `css` 动画中，我们通常设置一系列的关键帧，来实现不同时段的效果动画。当然，在 `motion` 中，这也是被允许的。通过数组的形式，来指定多段关键帧；如果未指定过渡时间，将会以平均分段的形式执行动画，因此你可以执行 `transition` 来指定不同关键帧的过渡时间段。

```vue
 <motion.div
      class="box"
      :animate="{ x: [null, 100, 0], scale: [0, 1, 0] }"
      :transition="{ duration: 3, times: [0, 0.2, 1] }"
  />
```

**variants**

在上诉案例中，我们在每个 `motion` 单独指定动画，但如果我们有多个 `motion` 想要执行相同的动画效果，单独指定岂不是十分乏力。

`variants` 可以允许我们指定特殊的动画效果。

```vue
<script setup>
import { motion } from 'motion-v'
const variants = {
  visible: { opacity: 1 },
  hidden: { opacity: 0 },
}
</script>

<template>
  <motion.div
      class="box1"
      :variants="variants"
      initial="visible"
      animate="hidden"
  />
  <motion.div
      class="box2"
      :variants="variants"
      initial="hidden"
      animate="visible"
  />
</template>

<style scoped>
.box1 {
  width: 100px;
  height: 100px;
  background-color: mediumspringgreen;
}
.box2 {
  width: 200px;
  height: 200px;
  background-color: cornflowerblue;
}
</style>
```

你可以通过数组的方式，同时引入多个 `variants`。此外 `variants` 通过 `motion` 组件向下流动，在父组件设置的 `variants` 子组件同样受到印象。

### Gestures 手势

是否还记得，我们在 `css` 动画中，可以通过 `hover` 伪元素来指定触发动画的时机，那在 `motion` 中他给我们提供了几个 `gesture`。

- `whileHover`
- `whilePress`
- `whileFocus`
- `whileDrag`
- `whileInView`

这几个 `gesture` 可以帮我们指定触发动画的时机。

```vue
 <motion.div
      class="box"
      drag
      :whileHover="{
        scale: 1.2,
        transition: { duration: 1 },
      }"
      :whilePress="{ scale: 0.9 }"
      :whileDrag="{ scale: 1.2, backgroundColor: '#f00' }"
  />
```

在拖拽时触发 `whileDrag` 会有一个默认的惯性动画，可以通过 `dragMomentum: false`  关闭，或者 `dragTransition` 设置过渡时间。

对于拖拽还有许多有趣的功能：

- 通过 `dragConstraints` 可以指定拖拽的范围，传入父元素的 `ref` 指定
- 通过 `dragDirectionLock` 可以锁定拖动的方向轴

### 滚动

使用过 `gsap` 的小伙伴都知道，`ScrollTrigger` 是一个非常强大的功能，在 `motion` 中，我们也能以最简单的方式都实现一个滚动动画；滚动触发的动画只是当元素进入或离开视口时触发的普通动画。

```vue
<div style="height: 200vh">
    <motion.div
        class="box"
        :initial="{ y: '2000px' }"
        :whileInView="{
            scale: 1.2,
            transition: { duration: 1 },
        }"
    />
</div>
```

使用 `whileInView` 当滚动值 `motion` 出现到视口中时，执行动画效果。

`inViewOptions` 针对于 `whileInView` 的一些配置选项：

- `once` 为 `true` 时，`motion` 多次出现在视口中时，动画只会执行第一次。
- `root` 指定父元素可滚动的元素，默认为 `window`
- `margin` 设置距离视口的边距
- `amount` 设置进入视口的状态 `some` `all` 或数字，当设置为 `all` 或 1时，当 `motion` 完全进入视口执行动画，值得注意的时需要将 `once` 设置为 `true` ，否则 `motion` 此时在视口中会不断执行动画

**滚动链接动画**

滚动链接动画是使用运动值和 `useScroll` 创建的。`useScroll` 有四个 `MotionValue`。

- `scrollX` `scrollY` 分别记录滚动偏移量
- `scrollXProgress` `scrollYProgress` 记录滚动的滚动进入，介于 0 到 1 之间

```vue
<script setup>
import { motion } from 'motion-v'
import { useScroll } from "motion-v"
const { scrollYProgress } = useScroll()
</script>

<template>
  <div style="height: 2000px">
    <motion.div class="box" :style="{ scaleX: scrollYProgress }" />
  </div>
</template>

<style scoped>
.box {
  width: 100%;
  height: 20px;
  position: fixed;
  top: 0;
  left: 0;
  background-color: mediumspringgreen;
}
</style>
```

`scrollXProgress` `scrollYProgress` 可以很好的帮助我们监听滚动的完成进度，从而实现一些于滚动条绑定的动画效果。

**平滑过渡**

上诉例子中，单纯使用 `scrollYProgress` 的值实现一个动画效果，显得有些许生硬。`motion` 也给我们提供了一个 `hooks` `useSpring` ，可以帮助我们实现一些平滑的过渡效果。

```vue
<script setup>
import { motion } from 'motion-v'
import { useScroll, useSpring } from "motion-v"
const { scrollYProgress } = useScroll()
const scaleX = useSpring(scrollYProgress, {
  stiffness: 100,
  damping: 30,
  restDelta: 0.001
})
</script>
<template>
  <div style="height: 2000px">
    <motion.div class="box" :style="{ scaleX: scaleX }" />
  </div>
</template>
```

**useScroll 参数**

- `container` 指定跟着滚动的容器，未指定默认为 `window`
- `target` 指定滚动的目标元素，未指定时与 `container` 一直
- `axis` 监听的滚动方向，默认为 `y`
- `offset` 触发回调的偏移量默认值 `["start start", "end end"]`，参数的格式为 `["元素边界 容器边界", "元素边界 容器边界"]`
  - `["start start", "end end"]` 当元素的顶部触碰到容器的顶部动画开始，元素的底部触碰到容器的底部动画结束（见下图）
  - 参数可以以字符串的形式例如 `start center end` 指定容器、元素的位置，也可以使用单位例如 `100px 20vh`
  - 参数同样也可以是数字 `0  0.5  1`

![scroll1](/images/motion-v-scroll1.jpg)

![scroll2](/images/motion-v-scroll2.jpg)

当然上述情况，开发者可以自定义的指定起始与结束位置，利用像素单位或者数值。

**useSpring**

```js
// useSpring 的第一个参数是运动数值，可以指定数值或像素单位
// 第二个参数则是配置选项 可以通过Transition props配置
const x = useSpring(0)
const y = useSpring("100vh")

// 更新运动数值，使用 set 更新，都会通过动画效果过渡到新数值
x.set(100)
y.set("50vh")

// 直接过渡，无动画效果
x.jump(50)
y.jump("0vh")
```

**layout**

在 `css` 动画中，有部分过渡效果我们没办法利用动画的形式表现出来，例如 `justify-content: flex-start` 切换到 `justify-content: flex-end`，而 `motion` 给我们提供了这个能力利用 `layout`，可以轻松实现这个过渡效果。

```vue
<script setup>
import { motion } from 'motion-v'
import { ref } from 'vue'

const isOn = ref(false)

const toggleSwitch = () => {
  isOn.value = !isOn.value
}
</script>

<template>
  <button
      @click="toggleSwitch"
  >toggle
  </button>
  <motion.div :class="['toggle-container', isOn ? 'start' : 'end']">
    <motion.div
      :transition="{
          type: 'spring',
          visualDuration: 0.2,
          bounce: 0.2
      }"
      layout
      class="box"
    />
  </motion.div>

</template>

<style>
.toggle-container {
  width: 100px;
  height: 100px;
  background-color: mediumspringgreen;
  cursor: pointer;
  display: flex;
  padding: 10px;
}

.toggle-container.start {
  justify-content: flex-start;
}

.toggle-container.end {
  justify-content: flex-end;
}

.box {
  width: 30px;
  height: 30px;
  background: cornflowerblue;
}
</style>

```

**请注意，CSS 更改应通过 `style` 立即发生，而不是 `animate` ，因为 `layout` 将负责动画。**

`layout` 的利用场景非常广泛，例如网格布局，重新排序列表，新增\删除项目等。

### Transitions 过渡

`transition` 过渡在 `motion` 动画中至关重要，动画的本质其实就是由一种状态到另一种状态的变化，而让我们肉眼能感受到过渡过程则是由 `transition`，例如过渡的效果、时间等。

**设置过渡**

你可以在任何的 `props` 属性上设置 `transition`， 当然你也可以直接将 `transition` 作为一个 `props`。

```vue
<!-- 直接作为 props -->
<motion.div
  :transition="{
    type: 'spring',
    visualDuration: 0.2,
    bounce: 0.2
  }"
/>

<!-- 在其他 props 中使用 transition -->
<motion.div
  :whileHover="{
    scale: 1.1,
    transition: { duration: 0.2 }
  }"
/>
<motion.div
  :animate="{
    x: 0,
    opacity: 1,
    transition: {
      default: { type: 'spring' },
      opacity: { ease: 'linear' }
    }
  }"
/>
```

**Transition config**

`type`：`motion` 中内置了三种运动类型，用户可以直接指定类型来执行过渡效果。

- `tween` 补间动画，受 `duration` 和 `easing` 参数影响
- `spring` 弹簧动画，受  `duration` 或者基础物理（基于基础物理的持续效果提供了物理数值如下，然后通过一系列的数学计算得出过渡效果， 或者设置 `bounce` 0 - 1，来决定是否有弹簧效果）
  - `stiffness` 刚度，弹簧的刚度。值越高，产生的运动越突然。
  - `damping `阻尼，反作用力的强度。如果设置为 0，弹簧将无限振荡。
  - `mass` 质量，运动物体的质量。值越高，运动越迟缓 （如果设置了以上三个物理数值，`bounce` 和 `duration` 将被覆盖）
  - `velocity` 弹簧的初始速度
  - `visualDuration` 视觉上的持续时间，将会覆盖 `uration`
- `inertia` 惯性动画，与弹簧动画类似，都是通过一系列的数学公式计算得出过渡效果，但与 `tween` 和 `spring` 不同的是，单独设置 `type: inertia` 不会有运动效果，必须提供必要的数值进行计算
  - `power` 初始动力，动力越高，速度越快
  - `timeConstant` 速度的衰减系数，数值越高，速度衰减得越快
  - `modifyTarget` 当惯性不住导致物体未到达指定位置，可以由该参数进行修正
  - `min` `max` （想象一下，当我们向上往下扔下一个小球， 小球触地后会有回弹效果，知道小球动力为0，停止到地面），`min` 和 `max` 就是设置回弹效果碰撞最大最小位置，好比建立了两堵墙，小球碰到墙就会回弹，但无论如何，小球的回弹都不出超出两块墙壁的位置。
  - `bounceStiffness` 如果设置了 `min` 或 `max` ，则会影响弹跳弹簧的硬度。（还是以小球为例，该值越高，小球回弹的速度越快）
  - `bounceDamping` 阻尼

当然我们也不一定非要使用这三种动画效果，我们也可以自定义自己需要的过渡效果，一下的参数可以帮助你更快速的构建自己需要的过渡效果。

- `delay` 延迟执行动画效果
- `repeat` 重复次数，设置 `Infinity` 则重复执行
- `repeatType` 重复动画的方式
  - `loop` 从头开始执行，假设我们设置 x 由 `100px` 向 `500px` 运动，该重复方式是一次动画结束后只会重置还是由 `100px` 向 `500px` 运动
  - `reverse` 反向执行，一次动画结束后不会重置，运动轨迹 `100px -> 500px` `500px -> 100px` 反复运动，类似于回放效果
  - `mirror` 与 `reverse` 类似，但他不是回放效果，而是反复发变更起点与终点，相当于每次运动都是新的运动
- `repeatDelay` 每次重复动画之间延迟的时间
- `when` 指定父子动画之间的执行顺序 （下方有示例）
  - `beforeChildren` 子动画将在父动画完成后播放
  - `afterChildren` 父动画将在子动画完成后播放
- `delayChildren` 在父级上设置 `delayChildren` 将会将子动画延迟此持续时间
- `staggerChildren` 在父级上设置 `staggerChildren` 将使子级按此持续时间错开

```vue
<script setup>
import { motion } from 'motion-v'
const list = {
  hidden: {
    opacity: 0,
    transition: { when: "afterChildren", duration: 2, delay: 2 }
  }
}

const item = {
  hidden: {
    opacity: 0,
    transition: { duration: 2 }
  }
}
</script>

<template>
  <motion.ul :variants="list" animate="hidden">
    <motion.li :variants="item" />
    <motion.li :variants="item" />
  </motion.ul>
</template>

<style>

ul {
  width: 100px;
  height: 100px;
  background: greenyellow;
}

li {
  width: 100px;
  height: 40px;
  background: blueviolet;
}
</style>

```

## components

在动画章节，我们大概演示了 `motion-v` 的基本动画流程，他已经可以帮助我们完成绝大部分的动画效果。在这章节，我们介绍一些 `motion` 提供的内置组件，但有些组件我们已经使用过了例如，`<Motion />` `<AnimatePresence />` 这里就不做过多介绍，大家可以到官网查看更详细的 `props` 参数。也有些付费组件例如，`<AnimateNumber />` `<Cursor />` ，虽然功能强大，但毕竟付费，其实通过 `motion` 组件我们也能自己封装出来。

###  `LazyMotion`

```js
import { motion } from "motion-v" // ×
import { m } from "motion-v" // ✔ 引入 LazyMotion
```

`LazyMotion`  与 `motion` 类似，但捆绑的体积更小，但与 `motion` 不同的是， `m` 组件没有预装动画、布局动画或拖动手势等功能。

### `<MotionConfig />`

他允许同一设置所有 `motion` 子组件的配置选项，用于配置通用选项。

```vue
<script setup>
  import { motion, MotionConfig } from "motion-v"
</script>

<template>
  <MotionConfig :transition="{ duration: 1 }">
    <motion.div
        :initial="{ opacity: 0 }"
        :animate="{ opacity: 1 }"
      />
  </MotionConfig>
</template>
```

## motion value

`motion` 同样也内置了一些 `hooks` 钩子函数，用于存储和管理动画的实时值，能够自动处理运动数值，他们都返回的是 `motionValue`。例如我们之前也遇到过了的 `useScroll useSpring`，在这里我们也简单介绍一下。

### useMotionValue

```vue
<script setup>
  import { motion, useMotionValue } from "motion-v"
  const x = useMotionValue(0)
</script>

<template>
  <motion.div :style="{ x }" />
</template>
```

一般情况下，我们在设置一个动画值时，可以直接 `const x = '100px'`，也可以手动创建的方式 `const x = useMotionValue(100px)`，手动创建的优势是：

- 设置获取他们的状态
- 可以传递给多个组件，同步他们之间的运动
- 通过 `useTransform` 钩子链接 `MotionValue`
- 更新动画，而不触发 vue 的生命周期
- 订阅更新

```js
import { motion, useMotionValue } from "motion-v"
const x = useMotionValue(0)

// 设置
x.set(100)

// 获取
x.get()

// 获取运动的速度
x.getVelocity()

// 直接设置，不会执行动画效果，运动的初始速度为0
x.jump(10)

// 是否正在播放动画
isAnimating()

// 停止动画
stop()

// 订阅
on()

// 销毁订阅者
destroy()
```

### useMotionTemplate

`useMotionTemplate` 允许我们通过模板字符串的方式创建运动值。

```vue
<script setup>
import { useMotionTemplate } from "motion-v"
const blur = useMotionValue(10)
const saturate = useMotionValue(50)
const filter = useMotionTemplate`blur(${blur}px) saturate(${saturate}%)`
</script>

<template>
  <motion.div :style="{ filter }" />
</template>
```

### useMotionValueEvent

动画的事件监听，在 `vue` 生命周期内有效，当 `vue` 组件被卸载后，组件内的事件监听将被清理。

- `change` 动画数值变化
- `animationStart` 动画开始
- `animationComplete` 动画结束
- `animationCancel` 动画取消

```vue
<template>
  <motion.div
      class="box"
      :initial="{ x: 0 }"
      :animate="controls"
      :style="{ x }"
      :transition="{ duration: 1 }"
  />
  <button @click="start">开始动画</button>
  <button @click="cancel">取消动画</button>
</template>

<script setup>
import { useMotionValueEvent, motion, useAnimationControls, motionValue } from 'motion-v'

const controls = useAnimationControls()

const x = motionValue(0)
const start = () => {
  controls.start({ x: 100, transition: { duration: 0.5 } })
}

const cancel = () => {
  controls.stop()
}


useMotionValueEvent(x, 'change', () => {
  console.log('x', x.get())
})

useMotionValueEvent(x, 'animationStart', () => {
  console.log('animationStart')
})

useMotionValueEvent(x, 'animationComplete', () => {
  console.log('animationComplete')
})

useMotionValueEvent(x, 'animationCancel', () => {
  console.log('animationCancel')
})
</script>

<style>
.box {
  width: 100px;
  height: 100px;
  background-color: red;
}
</style>

```

`useMotionValueEvent` 是运动值 `on`  方法的辅助函数。使用 `on` ，您可以随时监听事件

```js
x.on('change', () => {
  console.log('x', x.get())
})

x.on('animationStart', () => {
  console.log('animationStart')
})

x.on('animationComplete', () => {
  console.log('animationComplete')
})

x.on('animationCancel', () => {
  console.log('animationCancel')
})
```

### useTransform

`useTransform` 创建一个新的运动值，用于转换一个或多个运动值的输出。`useTransform` 可以通过两种方式使用：使用转换函数和通过值映射：

**转换函数**

```vue
<template>
  <motion.div
      class="box"
      :initial="{ x: 1 }"
      :animate="{
        x: z.get()
    }"
      :transition="{ duration: 1 }"
  />
</template>

<script setup>
import { motion, useTransform, useMotionValue } from 'motion-v'
const x = useMotionValue(10)
const y = useMotionValue(10)

const z = useTransform(() => x.get() + y.get())
</script>

<style>
.box {
  width: 100px;
  height: 100px;
  background-color: red;
}
</style>
```

**值映射**

```vue
<template>
  <div>
    <motion.div :style="{ backgroundColor: color }" class="box"></motion.div>
    <input type="range" v-model="sliderValue" @input="handleChange" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { motionValue, useTransform } from 'motion-v'

const x = motionValue(0)
const sliderValue = ref(0)
const color = useTransform(x, [0, 100], ['#f00', '#00f'])

function handleChange(event) {
  const newValue = parseInt(event.target.value)
  x.set(newValue)
  sliderValue.value = newValue
}
</script>

<style scoped>
.box {
  width: 100px;
  height: 100px;
  border-radius: 5px;
}
</style>
```

### useTime

`useTime` 是一个用于**追踪时间进度**的 Hook，通常返回一个 `MotionValue`，表示从动画开始或组件挂载后经过的时间。

```vue
<template>
  <motion.div
    class="box"
    :initial="{ opacity: 1 }"
    :style="{ 
      opacity: opacityValue 
    }"
  />
</template>

<script setup>
import { motion, useTime, useTransform } from 'motion-v';

const time = useTime();
// 将时间映射到透明度 0~1 循环
const opacityValue = useTransform(
  time,
  (t) => Math.abs(Math.sin(t * Math.PI)) // 每秒淡入淡出一次
)
</script>
```

## 总结

**省流：非常好用。**

以上就是 `motion-v` 的基础用法，当你学会该库的用法后，对于 `motion/react` 也就得心应手了，他们拥有着相同的 `API` 以及功能。撰写该文章其实是有两个原因，其一是随着 `vue` 生态的逐渐完善，弥补了动画方便的不足，更好的帮助更多 `vuer` 入门，其二是由于文档中有 `motion+` 付费功能，因此文档并不开源，市面上也没有中文翻译的版本，这也是为了帮助更多国内同学更快的掌握学习。

**hold on 等等等**

对于当前的生态环境，文档的撰写语言都是以英语为主，这对国内英文不好的同学也造成了极大的不变。`antfu` 开发了一个 [nuxt 学习教程](https://learn.nuxt.com/en)，用于学习 Nuxt 的交互式教程和游乐场，但是目前官方文档中只有英文与日文，对此我们 `fork` 了一份，对其进行了中文翻译。

对于想要快速学习 `nuxt` 的同学可以前往学习，当然如果你发现有哪里翻译不足的地方，欢迎提交 `PR`。目前 [nuxt 学习教程](https://learn.nuxt.com/en) 也处于开发阶段，我们会第一时间更新官方内容，并对其进行中文翻译，保证内容的实时性。

- [中文翻译](https://learnnuxt.netlify.app/)
- [Github](https://github.com/ffgenius/learn.nuxt.com-zh)

