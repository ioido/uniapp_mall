<template>
	<view>
		<!-- 外卖商城固定表头 -->
		<view class="nav"></view>
		<view class="zw"></view>
		<view style="height: 200upx;background-color: #4CD964;"></view>
		<view class="main" :style="{height: mainHeight}">
			<view class="v3" style="height: 100upx;background-color: #007AFF;"></view>
			<view class="box1">
				<scroll-view class="left" scroll-y="true">
					<view @click="leftCellClick(item)" v-for="item in categoryList" :key="item.id" class="lc" :class="{active: item.id == currentId}">
						{{item.name}}
					</view>
				</scroll-view>
				<scroll-view class="right" scroll-y="true" :scroll-top="rightScrollTop" @scroll="rightScroll">
					<view class="goodsListBox">
						<view class="category" v-for="item in categoryList" :key="item.id" :id="'goodsBox'+item.id" >
							<view style="color: #795DA3;" class="s-item">{{item.name}}</view>
							<view class="list"  >
								<view class="box" v-for="(rowData,i) in goodsList" :key="rowData.id" v-if="item.id == rowData.type_id">
									<view class="m-store-item">
										<view class="m-text">
											<view class="m-title" style="height: 300upx;">
												{{rowData.name}}
											</view>
										</view>	
									</view>
								</view>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
		
	</view>
</template>

<script>
	
	import testData from '@/common/testdata.js'
	
	var statusBarHeight = uni.getSystemInfoSync().statusBarHeight;
	
	export default {
		data() {
			return {
				mainHeight: 0,
				categoryList: testData.categoryData,
				goodsList: testData.goodsData,
				currentId: 1,
				rightScrollTop: 0
			}
		},
		onLoad() {
			
		},
		onShow() {
			let sysInfo = uni.getSystemInfoSync();
			this.mainHeight = sysInfo.screenHeight - 80  + 'px';
			console.log(this.mainHeight)
		},
		methods: {
			leftCellClick(item) {
				this.currentId = item.id
				this.calsSize()
				let index = this.categoryList.findIndex(sitem=>sitem.id === item.id)
				this.rightScrollTop = this.categoryList[index].top;
				console.log(this.rightScrollTop)
			},
			rightScroll(e) {
				let that = this;
				this.calsSize();
				let scrollTop = Math.round(e.detail.scrollTop);
				let tabs = this.categoryList.filter(item=>item.top <= scrollTop).reverse();
				console.log(tabs)
				if (tabs.length>0) {
					this.currentId = tabs[0].id;
				}
			},
			//计算右侧栏每个tab的高度等信息
			calsSize(event) {
				let h = 0
				// console.log(this.categoryList)
				this.categoryList.forEach(item=>{
					let view = uni.createSelectorQuery().select('#goodsBox' + item.id);
					view.fields({
						size: true
					}, data=>{
						item.top = h;
						h += Math.round(data.height);
						item.bottom = h;
					}).exec();
				})
				// console.log(this.categoryList)
			}
		}
	}
</script>

<style lang="scss">
	.nav {
		width: 100%;
		overflow: hidden;
		position: fixed;
		top: 0;
		left: 0;
		height: 160upx;
		background-color: #FF0000;
		z-index: 99;
	}
	.zw {
		height: 160upx;
	}
	.main {
		background-color: yellow;
		position: relative;
		.box1 {
			background-color: blue;
			position: relative;
			display: flex;
			height: calc(100% - 50px);
		}
	}
	.left,.right {
		position: absolute;
		top: 0;
		bottom: 0;
	}
	.left {
		left: 0;
		width: 24%;
		background-color: #795DA3;
		.active {
			color: blue;
		}
	}
	.right {
		left: 24%;
		width: 76;
		background-color: #EE9900;
		.s-item {
			background-color: red;
			position: sticky;
			top: 0;
			z-index: 18;
		}
	}
	.lc {
		height: 120upx;
		background-color: yellow;
	}
</style>