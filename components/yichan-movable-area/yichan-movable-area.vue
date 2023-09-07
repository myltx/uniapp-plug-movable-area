<template>
	<view>
		<view class="slider-container">
			<view class="flex"></view>
			<movable-area class="sliderBar" :style="{width: (100)+'%'}" :damping="10">
				<view class="gone" :style="{width: x +'px'}"></view>
				<movable-view class="slider" direction="all" :x="x" @change="onChange">
					<text>{{ score }}</text>
				</movable-view>
			</movable-area>
			<view :style="{width: (minScore / maxScore) * 100 +'%'}"></view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			max: {
				type: Number,
				default: 10
			},
			min: {
				type: Number,
				default: 0
			},
			defaultValue: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				slideBarWidth: 0,
				minScore: this.min ? this.min : 0,
				maxScore: this.max ? this.max : 10,
				x: 0,
				y: 0,
				score: 0,
			};
		},
		mounted() {
			var that = this;
			console.log(this.defaultValue)
			// 解决报错问题
			let el = uni.createSelectorQuery().in(this)
			this.$nextTick(() => {
				el.select(".slider-container").boundingClientRect(function(res) {
					that.slideBarWidth = res.width
					// 根据 defaultValue 计算出初始位置和分数
					if (that.defaultValue) {
						that.score = that.defaultValue || 0
						console.log(that.defaultValue / that.maxScore * that.slideBarWidth)
						that.x = that.defaultValue / that.maxScore * that.slideBarWidth
						that.score = that.defaultValue
					}
				}).exec();
			})
		},
		methods: {
			onChange: function(e, i) {
				// 这里是为了防止设置默认值的时候重复触发
				if(!e.detail.source) return
				console.log(e.detail.x, this.slideBarWidth)
				this.x = e.detail.x
				this.score = parseInt(this.x / this.slideBarWidth * this.maxScore)
				this.$emit('change', this.score)
			}
		}
	}
</script>

<style lang="scss">
	$uni-color-primary: #3A84FA;

	.slider-container {
		display: flex;
		width: 100%;
		height: 32rpx;
		position: relative;

		&::before {
			content: '';
			position: absolute;
			height: 8rpx;
			border-radius: 8rpx;
			background-color: #EEEEEE;
			top: 50%;
			left: 0;
			transform: translateY(-50%);
			width: 100%;
		}

		.flex {
			flex: 1;
			height: 8rpx;
			border-radius: 8rpx 0 0 8rpx;
			background-color: $uni-color-primary;
			margin-top: 12rpx;
			position: relative;
			z-index: 1;
		}

		.sliderBar {
			height: 100%;
			border-radius: 8rpx;
			width: 100%;

			.gone {
				background-color: $uni-color-primary;
				height: 100%;
				position: absolute;
				left: 0;
				height: 8rpx;
				top: 12rpx;
				max-width: 100%;
				z-index: 1;
				border-radius: 0 8rpx 8rpx 0;
			}

			.slider {
				width: 0;
				height: 100%;
				position: relative;
				z-index: 2;

				&::after {
					content: '';
					position: absolute;
					border-radius: 16rpx;
					background-color: $uni-color-primary;
					width: 32rpx;
					height: 100%;
					transform: translatex(-50%);
				}

				text {
					position: absolute;
					width: 60rpx;
					color: white;
					border-radius: 14rpx;
					top: -140%;
					left: 50%;
					text-align: center;
					transform: translateX(-50%);
					background-color: $uni-color-primary;

					&::after {
						content: '';
						position: absolute;
						border: 6rpx solid transparent;
						border-top-color: $uni-color-primary;
						top: 99%;
						left: 50%;
						transform: translateX(-50%);
					}
				}
			}
		}
	}
</style>