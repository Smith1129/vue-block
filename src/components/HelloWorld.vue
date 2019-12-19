<template>
	<div class="allcontent">
		<div class="code">
			<!-- 拼图 -->
			<div class="code_puzzle">
				<div class="puzzle_holder">
					<div class="puzzle_bgimg">
						<img
							src="http://necaptcha.nosdn.127.net/3210a63ad00b42a888f4b975da8ba7a4.jpg"
							class="bgimg_bg"
						/>
						<img
							src="http://necaptcha.nosdn.127.net/89f8b83d456b42cc8a71dc5c4a464751.png"
							class="bgimg_saw"
							ref="saw"
						/>
					</div>
					<div class="puzzle_top">
						<div class="puzzle_refresh" @click="refesh"></div>
					</div>
				</div>
			</div>
			<!-- 拖动 -->
			<div class="code_touch">
				<div class="code_touchTxt">
					<div class="indicator" ref="indicator" :class="toucheState"></div>
					<div class="btn" @mousedown.stop="rangeMove" v-if="toucheState!='success'">
						<span ref="sliderIcon"></span>
					</div>
					<span class="txt" :class="toucheState">{{infoTxt}}</span>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'HelloWorld',
	data() {
		return {
			disX: 0,
			rangeStatus: false, //拖拽状态
			infoTxt: '向右拖动滑块填充拼图', //提示文字
			toucheState: 'normal' //验证状态
		};
	},

	methods: {
		//滑块移动
		rangeMove(e) {
			let ele = e.target;
			let startX = e.clientX;
			console.log(e)
			console.log(startX,'dd')
			let eleWidth = ele.offsetWidth;
			let parentWidth = ele.parentElement.offsetWidth;
			let MaxX = parentWidth - eleWidth;
			let swg = this.$refs.saw;
			let indicator = this.$refs.indicator;
			this.infoTxt = '';
			this.toucheState = 'normal';
			if (this.rangeStatus) {
				//不运行
				return false;
			}
			document.onmousemove = e => {
				let endX = e.clientX;
				this.disX = endX - startX;
				if (this.disX <= 0) {
					this.disX = 0;
				}
				if (this.disX >= MaxX) {
					//减去滑块的宽度,体验效果更好
					this.disX = MaxX;
				}
				let isLeft = this.disX > 243 ? 243 : this.disX;
				ele.style.transition = 'none';
				ele.style.transform = 'translateX(' + this.disX + 'px)';
				swg.style.transition = 'none';
				swg.style.transform = 'translateX(' + isLeft + 'px)';
				indicator.style.transition = 'none';
				indicator.style.width = this.disX + 40 + 'px';
				e.preventDefault();
			};
			document.onmouseup = () => {
				console.log(this.disX)
				if (this.disX > 81 || this.disX < 73) {
					this.rangeStatus = true;
					ele.style.transition = '.5s all';
					ele.style.transform = 'translateX(0)';
					swg.style.transition = '.5s all';
					swg.style.transform = 'translateX(0)';
					indicator.style.transition = '.5s all';
					indicator.style.transform = 'translateX(0)';
					indicator.style.width = 0 + 'px';
					//执行失败的函数
					this.infoTxt = '验证失败';
					this.toucheState = 'error';
					setTimeout(() => {
						this.refesh();
					}, 600);
				} else {
					let params = {
						jwtToken: this.videoToken
					};
					this.toucheState = 'success';
					this.infoTxt = '验证成功';
					indicator.style.width = 100 + '%';
					//执行成功的函数
				}
				document.onmousemove = null;
				document.onmouseup = null;
			};
		},
		// 刷新
		refesh() {
			this.rangeStatus = false;
			let indicator = this.$refs.indicator;
			let swg = this.$refs.saw;
			this.infoTxt = '向右拖动滑块填充拼图';
			this.toucheState = 'normal';
			this.disX = 0;
			swg.style.transition = 'none';
			swg.style.transform = 'translateX(' + this.disX + 'px)';
			indicator.style.transition = 'none';
			indicator.style.width = this.disX + '%';
		}
	},
	mounted() {},
	created() {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.allcontent {
	background: rgba(000, 000, 000, 0.6);
	width: 100%;
	height: 970px;
	position: relative;
	.code {
		width: 320px;
		height: 225px;
		position: absolute;
		top: 50%;
		left: 50%;
		margin-left: -160px;
		margin-top: -117.5px;
		border: 1px solid #e6e8eb;
		background: #fff;
		padding: 9px;
		box-sizing: border-box;
		.code_puzzle {
			height: 150px;
			padding-bottom: 15px;
			.puzzle_holder {
				pointer-events: auto;
				position: relative;
				padding-top: 50%;
				overflow: hidden;
				box-sizing: border-box;
				height: 100%;
				.puzzle_bgimg {
					pointer-events: auto;
					position: absolute;
					top: 0;
					left: 0;
					width: 100%;
					height: 100%;
					.bgimg_bg {
						pointer-events: auto;
						vertical-align: top;
						width: 100%;
						border-radius: 2px;
					}
					.bgimg_saw {
						position: absolute;
						left: 0;
						top: 0;
						width: auto;
						height: 100%;
						transform: translateZ(0);
						perspective: 1000;
						backface-visibility: hidden;
					}
				}
				.puzzle_top {
					position: absolute;
					right: 0;
					top: 0;
					max-width: 68px;
					.puzzle_refresh {
						float: right;
						width: 30px;
						height: 30px;
						margin-left: 4px;
						cursor: pointer;
						background-image: url(http://cstaticdun.126.net//2.12.1/images/icon_light.c72616a.png);
						background-position: 0 -299px;
						background-size: 32px 544px;
						&:hover {
							background-position: 0 -266px;
						}
					}
				}
			}
		}
		.code_touch {
			height: 40px;
			border-radius: 2px;
			line-height: 40px;
			color: #45494c;
			border: 1px solid #e4e7eb;
			background-color: #f7f9fa;
			font-size: 14px;
			.code_touchTxt {
				height: 40px;
				position: relative;
				text-align: center;
				.indicator {
					height: 40px;
					border-radius: 2px;
					width: 0;
					border: 1px solid transparent;
					position: absolute;
					top: -1px;
					left: -1px;
					&.normal {
						background: #d1e9fe;
					}
					&.error {
						background: #fce1e1;
					}
					&.success {
						background: #d2f4ef;
					}
				}
				.btn {
					position: absolute;
					top: 0;
					left: 0;
					height: 100%;
					background-color: #fff;
					box-shadow: 0 0 3px rgba(0, 0, 0, 0.3);
					cursor: pointer;
					transition: background 0.2s linear;
					width: 40px;
					border-radius: 2px;
					span {
						position: absolute;
						top: 50%;
						margin-top: -6px;
						left: 50%;
						margin-left: -6px;
						width: 14px;
						height: 10px;
						background-image: url(http://cstaticdun.126.net//2.12.1/images/icon_light.c72616a.png);
						background-position: 0 -26px;
						background-size: 32px 544px;
					}
				}
				.txt {
					position: absolute;
					top: 50%;
					left: 50%;
					transform: translate(-50%, -50%);
					&.success {
						color: #52ccba;
					}
					&.error {
						color: #f57a7a;
					}
				}
			}
		}
	}
}
</style>

