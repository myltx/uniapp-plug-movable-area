<template>
	<view class="content">
		<view class="text-area">
			<view class="title">{{title}}</view>
			<view class="sub-title">默认</view>
			<yichan-movable-area @change="onChange" />
			<text class="text-container">
				<text class="text">max: 10 </text>
				<text class="text">min: 0 </text>
				<text class="text">score: {{ score }}</text>
			</text>
			<div class="placeholder"></div>
			<view class="sub-title">自定义 max min </view>
			<yichan-movable-area max="100" min="10" @change="onChange1" />
			<text class="text-container">
				<text class="text">max: 100 </text>
				<text class="text">min: 10 </text>
				<text class="text">score: {{ score1 }}</text>
			</text>
			<div class="placeholder"></div>
			<view class="sub-title">动态自定义 max min </view>
			<yichan-movable-area :max="max" :min="min" @change="onChange1" />
			<text class="text-container">
				<text class="text">max: {{ max }} </text>
				<text class="text">min: {{ min }} </text>
				<text class="text">score: {{ score1 }}</text>
			</text>
			<div class="placeholder"></div>
			<view class="sub-title">默认值设置</view>
			<yichan-movable-area max="100" min="10" :defaultValue="30" @change="onChange1" />
			<text class="text-container">
				<text class="text">defaultValue: 30 </text>
				<text class="text">score: {{ score1 }}</text>
			</text>
			<div class="placeholder"></div>
			<view class="sub-title">多个循环</view>
			<view v-for="(score, index) in list" :key="index">				
				<yichan-movable-area :max="score.max" :min="score.min" :defaultValue="score.score" @change="(score) => {
					onChangeInd(score, index)
				}" />
				<view style="margin:0 0 30px 0">
					我是第{{index}}个,当前值：{{score.score}}
				</view>
			</view>
		</view>

	</view>
</template>

<script>
import YiChanMovableArea from '../../components/yichan-movable-area/yichan-movable-area.vue';
	export default {
		components: {
			YiChanMovableArea,
		},
		data() {
			return {
				title: '可拖拽进度条、滑动条、评分条示例',
				score: 0,
				score1: 0,
				list: [{
					max: 10,
					min:0,
					score: 10
				},{
					max: 50,
					min:0,
					score: 30
				},],
				max: 50,
				min:20,
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
			},
			onChangeInd(score, ind) {
				// console.log(score, ind)
				this.list[ind].score = score
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
			margin-bottom: 60rpx;
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
