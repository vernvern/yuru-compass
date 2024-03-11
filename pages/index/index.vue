<template>

	<view>
		<uni-row class="demo-uni-row">
			<uni-col :xs="12" :sm="6" :md="6" :lg="6" :xl="6" v-for="item of menu">
				<view class="demo-uni-col ">
					<navigator :url="'../../' + item.nav">
						<button type="primary" plain="true">{{ item.name }}</button>
					</navigator>
				</view>
			</uni-col>
		</uni-row>



	</view>


</template>

<script>
	export default {
		data() {
			return {

				menu: [{
					name: "太阳月亮轨迹",
					nav: "pages/2rd/sun-moon-path"
				}, {
					name:'关于',
					nav: 'pages/about/about'
				}],
			}
		},
		methods: {

		}
	}
</script>

<style>
	.demo-uni-row {
		margin-bottom: 20px;
		/* QQ、抖音小程序文档写有 :host，但实测不生效 */
		/* 百度小程序没有 :host，需要设置block */
		/* #ifdef MP-TOUTIAO || MP-QQ || MP-BAIDU */
		display: block;
		/* #endif */
	}

	/* 支付宝小程序没有 demo-uni-row 层级 */
	/* 微信小程序使用了虚拟化节点，没有 demo-uni-row 层级 */
	/* #ifdef MP-ALIPAY || MP-WEIXIN */
	/deep/ .uni-row {
		margin-bottom: 10px;
	}

	/* #endif */

	.demo-uni-col {
		height: 50px;
		padding: 10px;

		border-radius: 4px;
	}

	.dark_deep {
		background-color: #99a9bf;
	}

	.dark {
		background-color: #d3dce6;
	}

	.light {
		background-color: #e5e9f2;
	}
</style>