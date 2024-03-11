<template>
	<view class="container">
		
		<uni-card>
			<text>罗盘: {{direction}}</text><br>
			<text>陀螺仪:</text><br>
			<text>- {{rotationRateX}} </text><br>
			<text>- {{rotationRateY}} </text><br>
			<text>- {{rotationRateZ}} </text>
		</uni-card>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				direction: 0,
				rotationRateX: 0,
				rotationRateY: 0,
				rotationRateZ: 0,
			}
		},
		onLoad(){
			
			// 罗盘
			this.direction = 0
			
			const directioncallback = function (res) {
				console.log(res.direction);
				this.changedirection(res.direction)
			}
			uni.onCompassChange(directioncallback);
			uni.offCompassChange(directioncallback);
			
			
			// 陀螺仪
			this.rotationRateX = 0
			this.rotationRateY = 0
			this.rotationRateZ = 0
			
			
		},
		methods: {
			changedirection(direction) {
				this.direction = direction
			}
		}
	}
</script>

<style>
</style>