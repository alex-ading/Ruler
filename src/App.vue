<template>
	<div class="container" @contextmenu="handleMenu">
		<!-- <div class="component-container" style="height: 80px;">
			<div
				:style="{
					width: '80px',
					height: '80px',
					border: '1px solid black',
					cursor: 'pointer',
					position: 'absolute',
					top: top,
					left: left
				}"
				@mousedown="handleMousedown"
				@mouseup="handleMouseup"
				id="test"
			>
				<div>hhh</div>
			</div>
		</div>-->
		<Ruler :width="width" :height="height" :rulerWidth="10" :span="100" :gap="20" :spanColor="spanColor"
			:unit="unit" :showGrid="true" :ratio="ratio" :handleWheel="handleWheel"></Ruler>

		<div class="menu" :style="{
			top: menuTop,
			left: menuLeft,
			bottom: menuBottom,
			right: menuRight
		}">

		</div>
	</div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, reactive } from 'vue'
import Ruler from './components/Ruler.vue'

export default defineComponent({
	name: 'App',
	components: {
		Ruler
	},

	setup() {
		let width = ref(1000);
		let height = ref(600);
		let spanColor = ref('#b7b7b7');
		let unit = ref('mm');
		let ratio = ref(2)

		let startPos = reactive({
			left: 0,
			top: 0
		})
		let top = ref('0px');
		let left = ref('0px');
		let isMouseMove = ref(false);
		let isOut = ref(false);

		let menuTop = ref('0px');
		let menuLeft = ref('0px');
		let menuBottom = ref('auto');
		let menuRight = ref('auto');

		const handleMousedown = (e) => {
			startPos.left = e.clientX;
			startPos.top = e.clientY;
			isMouseMove.value = true;
		}

		const handleMousemove = (e) => {
			if (!isMouseMove.value) return
			e.preventDefault(); // 防止文字拖拽的现象产生
			let xGap = e.clientX - startPos.left, yGap = e.clientY - startPos.top;
			startPos.left = e.clientX;
			startPos.top = e.clientY;
			let topVal = parseInt(top.value) + yGap, leftVal = parseInt(left.value) + xGap;
			top.value = topVal + 'px';
			left.value = leftVal + 'px';
			if (topVal < 0 || leftVal < 0) { // 是否跑出可视区域，比如跑到工具栏上
				isOut.value = true;
			}
		}

		const handleMouseup = (e) => {
			isMouseMove.value = false;
			if (isOut.value) {
				isOut.value = false;
				if (parseInt(top.value) < 0) top.value = '0px';
				if (parseInt(left.value) < 0) left.value = '0px';
			}
		}

		const handleWheel = (e) => {
			if (e.ctrlKey) { // 是否按下 control 键
				e.preventDefault();
				e.stopPropagation();

				if (e.wheelDelta < 0) {
					// 缩小
					if (ratio.value <= 0.5) return
					ratio.value = ratio.value / 2;
				} else {
					if (ratio.value >= 16) return
					ratio.value = ratio.value * 2;
				}
			}
		}

		const handleMenu = (e) => {
			e.preventDefault();
			console.log();
			// 300 400
			let x = e.clientX, y = e.clientY;
			let width = document.body.clientWidth, height = document.body.clientHeight;
			console.log(width)
			if ((width - x) < 300) {
				if ((height - y) < 400) {
					menuLeft.value = (x - 300) + 'px';
					menuTop.value = (y - 400) + 'px';
				} else {
					menuLeft.value = (x - 300) + 'px';
					menuTop.value = y + 'px';
				}
			} else {
				if ((height - y) < 400) {
					menuLeft.value = x + 'px';
					menuTop.value = (y - 400) + 'px';
				} else {
					menuLeft.value = x + 'px';
					menuTop.value = y + 'px';
				}
			}

		}

		onMounted(() => {
			document.addEventListener('mousemove', handleMousemove);
			document.addEventListener('mouseup', handleMouseup);
			document.addEventListener('wheel', (e) => { e.stopPropagation() })
		})



		return {
			width,
			height,
			spanColor,
			unit,
			ratio,

			handleMousedown,
			handleMousemove,
			handleMouseup,

			top,
			left,

			handleWheel,

			handleMenu,

			menuTop,
			menuLeft,
			menuBottom,
			menuRight
		}
	}
})
</script>

<style>
body {
	margin: 0;
}

#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
}

.container {
	width: 800px;
	height: 600px;
	border: 1px solid black;
	overflow: auto;
	margin-left: 200px;
}

.menu {
	position: absolute;
	background-color: bisque;
	width: 300px;
	height: 400px;
}

.content {
	position: absolute;
	overflow: scroll;
}
</style>
