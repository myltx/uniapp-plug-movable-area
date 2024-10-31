## 可拖拽进度条、滑动条、评分条、movable-area

### 简介

- 使用 [movable-area](https://developers.weixin.qq.com/miniprogram/dev/component/movable-area.html) 实现可拖拽进度条、滑动条、评分条

- 具体使用可以跳转至 [uniapp插件地址](https://ext.dcloud.net.cn/plugin?id=6274) 查看

### Example
```
<template>
	<view>
		<yichan-movable-area />
	</view>
</template>
<script>
	import YiChanMovableArea from '../../components/yichan-movable-area/yichan-movable-area.vue';
	export default {
		components: {
			YiChanMovableArea
		},
	}
</script>

```
### Props
|  属性名  | 类型 | 默认值 | 说明 |
|  :----:  | :----:  | :----: | :----: |
| min  | Number | 最小值 | 0 |
| max  | Number | 最大值 | 0 |
| defaultValue  | Number | 默认值 | 0 |
| disabled  | Boolean | 默认值 | false |
| delay  | Number | 默认值 | 0.42 |
### Events
|  函数名  | 说明 | 返回值 |
| :----:  | :----: | :----: |
| change  | 拖动时触发 | 返回当前选择的数值 |
| updateScore  | 更新组件 score | 无 |


<!-- 发布时需要 将  components 文件夹复制到 release/yichan-movable-area 下，然后 将 yichan-movable-area 打包 上传即可 -->