<template>
  <view>
    <view class="slider-container">
      <view class="flex"></view>
      <movable-area
        class="sliderBar"
        :style="{ width: 100 + '%' }"
        :damping="10">
        <view
          class="gone"
          :style="{
            width: x + 'px',
            transition: `width ${delay}s ease`,
          }"></view>
        <movable-view
          class="slider"
          direction="all"
          :x="x"
          @touchend="onTouchEnd"
          @change="onChange"
          :disabled="disabled">
          <text>{{ score }}</text>
        </movable-view>
      </movable-area>
    </view>
  </view>
</template>

<script>
export default {
  props: {
    max: {
      type: Number,
      default: 10,
    },
    min: {
      type: Number,
      default: 0,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    defaultValue: {
      type: Number,
      default: 0,
    },
    delay: {
      type: Number,
      default: 0,
    },
    step: {
      type: Number,
      default: 1,
    },
  },
  data() {
    return {
      slideBarWidth: 0,
      minScore: this.min ? this.min : 0,
      maxScore: this.max ? this.max : 10,
      x: 0,
      y: 0,
      score: this.min || 0,
      isDragging: false, // 用来标记是否正在拖动
      updateX: 0, // 用来记录拖动过程中的 x 值
      useStep: this.step,
    };
  },
  watch: {
    step: {
      immediate: true,
      handler(newStep) {
        if (newStep <= 0 || !Number.isInteger(newStep)) {
          console.warn(
            `[yichan-movable-area] Invalid "step" value: ${newStep}. "step" must be a positive integer. It has been reset to 1.`
          );
          // 自动恢复为默认值
          this.useStep = 1;
        }
        // 如果 step 大于 max，限制 step 为 max
        if (newStep > this.maxScore) {
          console.warn(
            `[yichan-movable-area] Invalid "step" value: "step" value cannot be greater than "max". It has been reset to ${this.maxScore}.`
          );
          this.useStep = this.maxScore;
        }
      },
    },
  },
  mounted() {
    var that = this;
    // 解决报错问题
    let el = uni.createSelectorQuery().in(this);
    this.$nextTick(() => {
      el.select(".slider-container")
        .boundingClientRect(function (res) {
          that.slideBarWidth = res.width;
          // 根据 defaultValue 计算出初始位置和分数
          console.log(that.defaultValue, "this.defaultValue");
          let useDefaultValue = that.defaultValue;
          if (that.defaultValue) {
            // 如果默认值大于 最大值，则限制为最大值
            // 这里根据 最大最小值限制默认值大小
            if (that.defaultValue > that.maxScore) {
              useDefaultValue = that.maxScore;
              console.warn(
                ` [yichan-movable-area] Invalid "defaultValue" value: ${that.defaultValue}."defaultValue" must be less than or equal to "max". It has been reset to ${that.maxScore}.`
              );
            } else if (that.defaultValue < that.minScore) {
              useDefaultValue = that.minScore;
              console.warn(
                `[yichan-movable-area] Invalid "defaultValue" value: ${that.defaultValue}."defaultValue" must be greater than or equal to "min". It has been reset to ${that.minScore}.`
              );
            } else {
              useDefaultValue = that.defaultValue;
            }
            that.score = useDefaultValue || that.minScore;
            // 如果 step 大于 0，则将分数限制为 step 的倍数
            if (that.step > 0) {
              that.score = Math.round(that.score / that.step) * that.step;
              useDefaultValue = that.score;
              console.warn(
                `[yichan-movable-area] Invalid "defaultValue" value: ${that.defaultValue}."defaultValue" must be a multiple of "step". It has been reset to ${that.score}.`
              );
            }
            that.x =
              ((useDefaultValue - that.minScore) /
                (that.maxScore - that.minScore)) *
              that.slideBarWidth;
            that.score = useDefaultValue;
          }
        })
        .exec();
    });
  },
  methods: {
    onChange: function (e, i) {
      // 这里是为了防止设置默认值的时候重复触发
      if (!e.detail.source) return;
      let newX = e.detail.x;
      this.isDragging = true; // 开始拖动时设置为 true
      if (newX < 0) {
        newX = 0;
      } else if (newX > this.slideBarWidth) {
        newX = this.slideBarWidth;
      }
      if (
        newX <
        ((this.minScore - this.min) / (this.max - this.min)) *
          this.slideBarWidth
      ) {
        newX =
          ((this.minScore - this.min) / (this.max - this.min)) *
          this.slideBarWidth;
      } else if (
        newX >
        ((this.max - this.minScore) / (this.max - this.min)) *
          this.slideBarWidth
      ) {
        newX =
          ((this.max - this.minScore) / (this.max - this.min)) *
          this.slideBarWidth;
      }
      this.x = newX;
      this.updateX = newX;

      if (this.useStep == 1) {
        this.score = parseInt(
          (this.x / this.slideBarWidth) * (this.maxScore - this.minScore) +
            this.minScore
        );
        this.$emit("change", this.score);
      }
    },
    onTouchEnd() {
      this.isDragging = false;
      if (this.useStep > 1) {
        // 计算原始的分数
        const rawScore =
          (this.updateX / this.slideBarWidth) *
            (this.maxScore - this.minScore) +
          this.minScore;

        // 计算最接近的 `useStep` 值，确保跳到最近的合法位置
        const steppedScore =
          Math.round((rawScore - this.minScore) / this.useStep) * this.useStep +
          this.minScore;
        // 确保 score 在 minScore 和 maxScore 之间
        this.score = Math.min(
          Math.max(steppedScore, this.minScore),
          this.maxScore
        );

        // 计算滑块新位置，使滑块对准正确的 `useStep`
        setTimeout(() => {
          this.x =
            ((this.score - this.minScore) / (this.maxScore - this.minScore)) *
            this.slideBarWidth;
        }, 50);

        // 触发 change 事件
        this.$emit("change", this.score);
      }
    },
    updateScore(score) {
      if (!["string", "number"].includes(typeof score)) {
        throw new Error("score必须是字符串或数字");
      }
      if (typeof score === "string") {
        score = score * 1;
      }
      if (score > this.maxScore) {
        throw new Error("score不能大于maxScore");
      }
      if (score < this.minScore) {
        throw new Error("score不能小于minScore");
      }
      this.x =
        ((score - this.minScore) / (this.maxScore - this.minScore)) *
        this.slideBarWidth;
      this.$nextTick(() => {
        this.score = score;
      });
    },
  },
};
</script>

<style lang="scss">
$uni-color-primary: #3a84fa;

.slider-container {
  display: flex;
  width: 100%;
  height: 32rpx;
  position: relative;

  &::before {
    content: "";
    position: absolute;
    height: 8rpx;
    border-radius: 8rpx;
    background-color: #eeeeee;
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
        content: "";
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
          content: "";
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

  .leftLimit {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.2);
    z-index: 0;
    border-radius: 8rpx 0 0 8rpx;
  }

  .rightLimit {
    position: absolute;
    top: 0;
    right: 0;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.2);
    z-index: 0;
    border-radius: 0 8rpx 8rpx 0;
  }
}
</style>
