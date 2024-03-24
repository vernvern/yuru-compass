<template>
	<view class="container">

		<canvas style="width: auto; height: 370px;" canvas-id="path"></canvas>

		<uni-card class="card">
			<text>罗盘: {{direction}}</text><br>
			<text>陀螺仪xyz: {{rotationRateX}} {{rotationRateY}} {{rotationRateZ}} </text><br>

		</uni-card>



	</view>
</template>

<script>
	export default {

		data() {
			return {
				direction: 0, // 罗盘

				// 陀螺仪
				rotationRateX: 0,
				rotationRateY: 0,
				rotationRateZ: 0,

				// canvas
				rotate: 0,
			}
		},


		beforeUnmount() {
			// 罗盘
			uni.offCompassChange(this.onCompassChange);
			console.info("offCompassChange");

			// 陀螺仪
			uni.stopGyroscope({
				success() {
					console.log('stopGyroscope success!')
				},
				fail() {
					console.log('stopGyroscope fail!')
				}
			})

			// 画图
			clearInterval(this.timer);

			console.info("beforeUnmount");
		},
		// 组建挂载后
		mounted() {
			console.info("mounted");


			const info = uni.getSystemInfoSync();
			this.radius = info.screenWidth / 3 - 10;

			// 罗盘
			this.direction = 0;
			console.info("onCompassChange");
			uni.onCompassChange(this.onCompassChange);

			// 陀螺仪
			this.rotationRateX = 0;
			this.rotationRateY = 0;
			this.rotationRateZ = 0;
			// 陀螺仪事件
			uni.onGyroscopeChange(this.onRotationChange)
			// 陀螺仪事件
			uni.startGyroscope({
				interval: "normal",
				success() {
					console.log('startGyroscope success')
				},
				fail() {
					console.log('startGyroscope fail')
				}
			})

			// 画图的定时器
			const second = 1000
			this.timer = setInterval(this.drawPath, second * 0.3);

			this.drawPath(); // 提前画出来一帧，显得没那么卡顿
		},

		methods: {

			// 罗盘值更新
			onCompassChange: function(res) {
				this.direction = res.direction;
			},

			// 陀螺仪值更新
			onRotationChange: function(res) {
				const len = 4;
				this.rotationRateX = res.x.toFixed(len);
				this.rotationRateY = res.y.toFixed(len);
				this.rotationRateZ = res.z.toFixed(len);
			},


			canvasIdErrorCallback: function(e) {
				console.error(e.detail.errMsg)
			},

			// 球的点位，上北下南左西右东
			createSphere: function(total = 10) {
				const points = [];
				let lat, lon, x, y, z;
				for (let i = 0; i <= total; ++i) {
					lat = i * Math.PI / total; // 纬度

					for (let j = 0; j <= total; ++j) {
						lon = j * 2 * Math.PI / total; // 经度

						x = Math.sin(lat) * Math.cos(lon);
						y = Math.sin(lat) * Math.sin(lon);
						z = Math.cos(lat);
						points.push([y, z, x]); // 更换位置

					}
				}
				return points;
			},
			// 3d to 2d
			xyzto2d: function(points) {
				const xy = points.map((point) => {
					const x = point[0];
					const y = point[1];
					const z = point[2];
					const zToD = 2 - z;
					return [x / zToD, y / zToD];
				})
				return xy
			},
			// 旋转
			rotating: function(rotateX, rotateY, rotateZ, points) {
				// 绕 y 轴旋转
				let result = points.map((point) => {
					const x = point[0] * Math.cos(rotateY) + point[2] * Math.sin(rotateY);
					const y = point[1];
					const z = -1 * point[0] * Math.sin(rotateY) + point[2] * Math.cos(rotateY);

					return [x, y, z];
				})
				// 绕 z 轴旋转
				// result = points.map((point) => {
				// 	const x = -1 * point[1] * Math.sin(rotateZ) + point[0] * Math.cos(rotateZ);
				// 	const y = point[1] * Math.cos(rotateZ) + point[0] * Math.sin(rotateZ);
				// 	const z = point[2];
				// 	return [x, y, z];
				// })
				
				// 绕 x 轴旋转
				// result = points.map((point) => {
				// 	const x = point[0] ;
				// 	const y = -1 * point[2] * Math.sin(rotateX) + point[1] * Math.cos(rotateX);
				// 	const z = point[2] * Math.cos(rotateX) + point[1] * Math.sin(rotateX);
				// 	return [x, y, z];
				// })
				

				

				return result;
			},
			// 画图
			drawPath: function() {
				this.rotate += 0.01; // 旋转  TODO 这个应该可以设成罗盘或者陀螺仪的参数

				const info = uni.getSystemInfoSync();
				const centerX = info.screenWidth / 2;
				const conterY = this.radius + 100;

				// console.info("drawPath", this.rotate, centerX, conterY, points.length);

				var ctx = uni.createCanvasContext('path');
				ctx.setLineWidth(0.005);

				ctx.translate(centerX, conterY); // 移动到中间

				var points = this.createSphere(30); // 计算在球上的点位
				points = this.rotating(this.rotate, this.rotate, this.rotate, points); // 旋转
				const xys = this.xyzto2d(points); // 3d to 2d 

				// 画图
				ctx.scale(this.radius * 2, this.radius * 2);
				ctx.beginPath();
				ctx.moveTo(xys[0][0], xys[0][1]);

				for (let i = 1, l = xys.length; i < l; i++) {
					ctx.lineTo(xys[i][0], xys[i][1]);
				}

				ctx.stroke();
				ctx.restore();
				ctx.draw();
			}
		},
	}
</script>

<style>
	.card {
		margin: 50px;
	}
</style>