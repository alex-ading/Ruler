<script lang="tsx">
import { defineComponent, onMounted, reactive, ref, watch } from 'vue'

export default defineComponent({
    name: 'Ruler',
    props: {
        width: {
            type: Number,
            default: 800
        },
        height: {
            type: Number,
            default: 800
        },
        borderColor: {
            type: String,
            default: '#666666'
        },

        rulerWidth: {
            type: Number,
            default: 20
        },
        rulerColor: {
            type: String,
            default: '#999999'
        },

        span: {
            type: Number,
            default: 100
        },
        gap: {
            type: Number,
            default: 25
        },
        gapColor: {
            type: String,
            default: '#e7e7e7'
        },
        spanColor: {
            type: String,
            default: '#b7b7b7'
        },

        unit: {
            type: String,
            defualt: 'px'
        },

        showGrid: {
            type: Boolean,
            default: true
        }
    },
    setup(props) {
        let canvas = reactive(null);
        let ctx = reactive(null);
        let ratio = ref(2);

        onMounted(() => {
            canvas = document.getElementById('canvas');
            ctx = canvas.getContext('2d');
            drawContainer(ctx, props.width, props.height, props.rulerWidth, props.span, props.gap, props.rulerColor, props.spanColor, props.gapColor, props.showGrid);
        })

        watch(props, (newProps, preProps) => {
            drawContainer(ctx, newProps.width, newProps.height, newProps.rulerWidth, newProps.span, newProps.gap, newProps.rulerColor, newProps.spanColor, newProps.gapColor, newProps.showGrid);
        })

        /**
         * 绘制画布+尺子
         * 
         * @param {} ctx
         * @param {number} width 画布宽
         * @param {number} height 画布高
         * @param {number} rulerWidth 尺子宽
         * @param {number} span 大间距
         * @param {number} gap 大间距的中的小间距
         * @param {string} rulerColor 尺子颜色
         * @param {string} spanColor 大间距处网格的颜色
         * @param {string} gapColor 小间距处网格的颜色
         * */
        const drawContainer = (ctx, width: number, height: number, rulerWidth: number, span: number, gap: number, rulerColor: string, spanColor: string, gapColor: string, showGrid: boolean) => {
            if (props.unit === 'mm') {
                width = ratio.value * width;
                height = ratio.value * height;
                rulerWidth = ratio.value * rulerWidth;
                span = ratio.value * span;
                gap = ratio.value * gap;
            }
            canvas.setAttribute('width', width + rulerWidth);
            canvas.setAttribute('height', height + rulerWidth);
            console.log('drawContainer');
            ctx.beginPath();
            drawHorizontalRuler(ctx, width, rulerWidth, span, gap, rulerColor);
            drawVerticalRuler(ctx, height, rulerWidth, span, gap, rulerColor);
            drawBorder(ctx, width, height, rulerWidth);
            if (showGrid) {
                drawGrid(ctx, width, height, rulerWidth, span, gap, spanColor, gapColor);
            }
        }

        /**
         * 绘制水平尺子
         * 
         * @param {} ctx 上下文
         * @param {number} width 画布宽
         * @param {number} rulerWidth 尺子宽度
         * @param {number} span 大间距
         * @param {number} gap 大间距的中的小间距
         * @param {string} rulerColor 尺子颜色
         * */
        const drawHorizontalRuler = (ctx, width: number, rulerWidth: number, span: number, gap: number, rulerColor: string) => {
            console.log('drawHorizontalRuler');
            let spanRound = Math.floor(width / span);
            let gapRound = span / gap; // 整除
            ctx.strokeStyle = rulerColor;
            ctx.moveTo(rulerWidth, 0);

            for (let i = 0; i <= spanRound; i++) {
                // 长刻度
                let str = String(span * i / ratio.value);
                ctx.fillText(str, rulerWidth + span * i + 3, rulerWidth * 0.8);
                ctx.lineTo(rulerWidth + span * i, rulerWidth);

                // 中间短刻度
                for (let j = 0; j < gapRound - 1; j++) {
                    ctx.moveTo(rulerWidth + span * i + gap * (j + 1), rulerWidth / 2);
                    ctx.lineTo(rulerWidth + span * i + gap * (j + 1), rulerWidth);
                }

                ctx.moveTo(rulerWidth + span * (i + 1), 0);
            }

            // 水平尺子底线
            ctx.moveTo(rulerWidth, rulerWidth);
            ctx.lineTo(rulerWidth + width, rulerWidth);
            ctx.stroke();
        }

        /**
         * 绘制竖直尺子
         * 
         * @param {} ctx 上下文
         * @param {number} height 画布高
         * @param {number} rulerWidth 尺子宽度
         * @param {number} span 大间距
         * @param {number} gap 大间距的中的小间距
         * @param {string} rulerColor 尺子颜色
         * */
        const drawVerticalRuler = (ctx, height: number, rulerWidth: number, span: number, gap: number, rulerColor: string) => {
            console.log('drawVerticalRuler');
            let spanRound = Math.floor(height / span);
            let gapRound = span / gap; // 整除

            ctx.strokeStyle = rulerColor;
            ctx.moveTo(0, rulerWidth);
            for (let i = 0; i <= spanRound; i++) {
                // 长刻度
                // ctx.save();
                let str = String(span * i / ratio.value);
                // ctx.rotate(20 * Math.PI / 180);
                ctx.fillText(str, 0, rulerWidth + span * i - 3);
                // ctx.restore();
                ctx.lineTo(rulerWidth, rulerWidth + span * i);


                // 中间短刻度
                for (let j = 0; j < gapRound - 1; j++) {
                    ctx.moveTo(rulerWidth / 2, rulerWidth + span * i + gap * (j + 1));
                    ctx.lineTo(rulerWidth, rulerWidth + span * i + gap * (j + 1));
                }

                ctx.moveTo(0, rulerWidth + span * (i + 1));
            }

            // 竖直尺子底线
            ctx.moveTo(rulerWidth, rulerWidth);
            ctx.lineTo(rulerWidth, rulerWidth + height);
            ctx.stroke();
        }

        /**
         * 绘制网格
         * 
         * @param {} ctx 上下文
         * @param {number} width 画布宽
         * @param {number} height 画布高
         * @param {number} rulerWidth 尺子宽
         * @param {number} span 大间距
         * @param {number} gap 大间距的中的小间距
         * @param {string} spanColor 大间距处网格的颜色
         * @param {string} gapColor 小间距处网格的颜色
         * */
        const drawGrid = (ctx, width: number, height: number, rulerWidth: number, span: number, gap: number, spanColor: string, gapColor: string) => {
            console.log('drawGrid');
            // 水平网格
            let widthSpanRound = Math.floor(height / span);
            let widthGapRound = span / gap; // 整除
            for (let i = 0; i <= widthSpanRound; i++) {
                // 浅色
                ctx.beginPath();
                ctx.strokeStyle = gapColor;
                for (let j = 0; j < widthGapRound - 1; j++) {
                    ctx.moveTo(rulerWidth, i * span + (j + 1) * gap + rulerWidth);
                    ctx.lineTo(rulerWidth + width, i * span + (j + 1) * gap + rulerWidth);
                }
                ctx.stroke();

                // 深色
                ctx.beginPath();
                ctx.strokeStyle = spanColor;
                ctx.moveTo(rulerWidth, (i + 1) * span + rulerWidth);
                ctx.lineTo(rulerWidth + width, (i + 1) * span + rulerWidth);
                ctx.stroke();
            }

            // 竖直网格
            let heightSpanRound = Math.floor(width / span);
            let heightGapRound = span / gap; // 整除
            for (let i = 0; i <= heightSpanRound; i++) {
                // 浅色
                ctx.beginPath();
                ctx.strokeStyle = gapColor;
                for (let j = 0; j < heightGapRound - 1; j++) {
                    ctx.moveTo(i * span + (j + 1) * gap + rulerWidth, rulerWidth);
                    ctx.lineTo(i * span + (j + 1) * gap + rulerWidth, rulerWidth + height);
                }
                ctx.stroke();

                // 深色
                ctx.beginPath();
                ctx.strokeStyle = spanColor;
                ctx.moveTo((i + 1) * span + rulerWidth, rulerWidth);
                ctx.lineTo((i + 1) * span + rulerWidth, rulerWidth + height);
                ctx.stroke();
            }
        }

        const drawBorder = (ctx, width: number, height: number, rulerWidth: number) => {
            ctx.moveTo(0, 0);
            ctx.lineTo(width + rulerWidth, 0);
            ctx.lineTo(width + rulerWidth, height + rulerWidth);
            ctx.lineTo(0, height + rulerWidth);
            ctx.lineTo(0, 0);
            ctx.stroke();
        }
        /**
         * 清空画布
         * 
         * @param {} ctx 上下文
         * @param {number} width 画布宽
         * @param {number} height 画布高        
         * */
        const clearCanvas = (ctx, width: number, height: number) => {
            console.log('clearCanvas');
            ctx.clearRect(0, 0, width, height);
        }

        // /**
        //  * 滚动放大缩小
        //  * 
        //  * 
        //  * */
        // const zoom = function(event) {
        //     console.log(event.wheelDelta);
        // }

        return {
            ratio
        }
    },
    render() {
        return (
            <>
                <div style={{
                    width: this.$props.width * this.ratio + this.$props.rulerWidth * this.ratio + 'px',
                    height: this.$props.height * this.ratio + this.$props.rulerWidth * this.ratio + 'px'
                }}>
                    <canvas id="canvas"></canvas>
                </div>
            </>
        )
    }

})
</script>