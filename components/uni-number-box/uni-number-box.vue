<template>
	<view class="uni-numbox">
		<view @click="_calcValue('minus')" class="uni-numbox__minus" :class="{ 'uni-numbox__minus--disabled': inputValue <= min || disabled }">
			<p class="uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue <= min || disabled }">-</p>
		</view>
		<input :disabled="disabledInput" @blur="_onBlur" class="uni-numbox__value" type="number" v-model="inputValue" />
		<view @click="_calcValue('plus')" class="uni-numbox__plus" :class="{ 'uni-numbox__plus--disabled': inputValue >= max || disabled }">
			<p class="uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue >= max || disabled }">+</p>
		</view>
	</view>
</template>
<script>
	export default {
		name: "UniNumberBox",
		props: {
			value: {
				type: [Number, String],
				default: 1
			},
			min: {
				type: Number,
				default: 0
			},
			max: {
				type: Number,
				default: 100
			},
			step: {
				type: Number,
				default: 1
			},
			disabled: {
				type: Boolean,
				default: false
			},
			disabledInput: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				inputValue: 0
			};
		},
		watch: {
			value(val) {
				this.inputValue = +val;
			},
			inputValue(newVal, oldVal) {
				if (+newVal !== +oldVal) {
					this.$emit("change", newVal);
				}
			}
		},
		created() {
			this.inputValue = +this.value;
		},
		methods: {
			_calcValue(type) {
				if (this.disabled) {
					return;
				}
				const scale = this._getDecimalScale();
				let value = this.inputValue * scale;
				let step = this.step * scale;
				if (type === "minus") {
					value -= step;
					if (value < this.min) {
						return;
					}
					if(value > this.max){
						value = this.max
					}
				} else if (type === "plus") {
					value += step;
					if (value > this.max) {
						return;
					}
					if(value < this.min){
						value = this.min
					}
				}

				this.inputValue = String(value / scale);
			},
			_getDecimalScale() {
				let scale = 1;
				// 浮点型
				if (~~this.step !== this.step) {
					scale = Math.pow(10, (this.step + "").split(".")[1].length);
				}
				return scale;
			},
			_onBlur(event) {
				let value = event.detail.value;
				if (!value) {
					// this.inputValue = 0;
					return;
				}
				value = +value;
				if (value > this.max) {
					value = this.max;
				} else if (value < this.min) {
					value = this.min;
				}
				this.inputValue = value;
			}
		}
	};
</script>
<style lang="scss" scoped>
	$box-height: 70upx;
	/* #ifdef APP-NVUE */
	$box-line-height: 70upx;
	/* #endif */
	$box-line-height: 70upx;
	$box-width: 70upx;

	.uni-numbox {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		height: $box-height;
		line-height: $box-height;
		width: 260upx;
	}

	.uni-numbox__value {
		background-color: $uni-bg-color;
		width: 120upx;
		height: $box-height;
		text-align: center;
		font-size: $uni-font-size-lg;
		border-width: 1rpx;
		border-style: solid;
		border-color: $uni-border-color;
		border-left-width: 0;
		border-right-width: 0;
	}

	.uni-numbox__minus {
		width: $box-width;
		height: $box-height;
		line-height: $box-line-height;
		text-align: center;
		font-size: 20px;
		color: $uni-text-color;
		background-color: $uni-bg-color-grey;
		border: 0 none;
	}

	.uni-numbox__plus {
		width: $box-width;
		height: $box-height;
		line-height: $box-line-height;
		text-align: center;
		font-size: 20px;
		color: $uni-text-color;
		background-color: $uni-bg-color-grey;
		border: 0 none;
	}

	.uni-numbox--text {
		font-size: 60upx;
		color: $uni-text-color;
		line-height: 62upx;
	}
	
	.uni-numbox__minus--disabled {
		background-color: #F2F2F2 !important;
	}
	
	.uni-numbox__minus--disabled {
		background-color: #F2F2F2 !important;
	}

	.uni-numbox--disabled {
		color: $uni-text-color-disable;
	}
</style>
