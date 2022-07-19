<template>
	<view class="content">
		<view class="text-area">
			<view class="title">{{title}}</view>
			<view class="sub-title">默认</view>
			<myltx-movable-area @change="onChange" />
			<text class="text-container">
				<text class="text">max: 10 </text>
				<text class="text">min: 0 </text>
				<text class="text">score: {{ score }}</text>
			</text>
			<div class="placeholder"></div>
			<view class="sub-title">自定义 max min </view>
			<myltx-movable-area max="100" min="10" @change="onChange1" />
			<text class="text-container">
				<text class="text">max: 100 </text>
				<text class="text">min: 10 </text>
				<text class="text">score: {{ score1 }}</text>
			</text>
		</view>

	</view>
</template>

<script>
	import MyltxMovableArea from '../../components/myltx-movable-area/myltx-movable-area.vue';
	export default {
		components: {
			MyltxMovableArea,
		},
		data() {
			return {
				title: '可拖拽进度条、滑动条、评分条示例',
				score: 0,
				score1: 0,
			}
		},
		onLoad() {

		},
		methods: {
			onChange(score) {
				console.log(score)
				this.score = score
			},
			onChange1(score) {
				console.log(score)
				this.score1 = score
			}
		}
	}
</script>

<style lang="scss">
	.content {
		width: 80%;
		margin: 0 auto;
		padding: 10rpx 30rpx;

		.text-container {
			.text {
				margin-right: 20px;
			}
		}

		.placeholder {
			padding: 20rpx;
		}

		.sub-title {
			margin-bottom: 80rpx;
			font-size: 36rpx;
			color: #8f8f94;
		}

		.title {
			margin-bottom: 20px;
			font-weight: bold;
			text-align: center;
			font-size: 36rpx;
			color: #333;
		}
	}
</style>
