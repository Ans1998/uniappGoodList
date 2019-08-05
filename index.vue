<style>
.true {
	border: 8px solid red;
}
.false {
	border: 2px solid #ccc;
}
.item {
  width:50px;
  height: 50px;
  border-radius: 100%;
}
/* 购物车动画 */
.shoppingCartAnimations {
	animation: shoppingCartAnimation 1s;
}
@keyframes shoppingCartAnimation {
    0% {
        opacity: 0;
        transform: scale3d(.3,.3,.3)
    }

    20% {
        transform: scale3d(1.1,1.1,1.1)
    }

    40% {
        transform: scale3d(.9,.9,.9)
    }

    60% {
        opacity: 1;
        transform: scale3d(1.03,1.03,1.03)
    }

    80% {
        transform: scale3d(.97,.97,.97)
    }

    to {
        opacity: 1;
        transform: scaleX(1)
    }
}

.but {
	margin: 10px;
	height: 60px;
	width: 80px;
	display: flex;
}

.ball {
	height: 40px;
	width: 40px;
	background: #5EA345;
	border-radius: 50%;
	position: fixed;
}

/* 从右到左 */
.delRightShoppingCardAnimations {
	animation: delRightShoppingCardAnimation .5s;
}
@keyframes delRightShoppingCardAnimation {
	from {
		transform: translateX(100px) rotate(900deg);
		animation-timing-function: linear;
	}
	to {
	    transform: translateX(0px) rotate(0);
		
	}
}
/* 从左到右 */
.delLeftShoppingCardAnimations {
	animation: delLeftShoppingCardAnimation .5s;
}
@keyframes delLeftShoppingCardAnimation {
	from {
		transform: translateX(0px) rotate(0);
	}
	to {
	    transform: translateX(100px) rotate(900deg);
	    animation-timing-function: linear;
	}
}
</style>
<template>
	<view id="test">
		
		<view>
			
			<!-- 头部 -->
			<view style="height: 150px; width: 100%;">
				<image style="height: 150px; width: 100%;" src="https://app.dev.9kbs.com/uploads/images/20170624/1498302003261252.png"></image>
			</view>
			
			<!-- 内容 -->
			<view class="flex">
				<scroll-view scroll-y scroll-with-animation style="height:calc(100vh - 400upx)">
					<!-- 左边 -->
					<view class="flex-sub bg-pink">
						<view :class="index == curListIndex ? 'true': 'false'" @tap="goAnchor"  :data-id="item.id" class="padding-tb-sm" v-for="(item, index) in leftNav" :key="index">
							<text>{{item.name}}</text>
						</view>
					</view>
				</scroll-view>
				
				<scroll-view scroll-y scroll-with-animation style="height:calc(100vh - 400upx)" :scroll-into-view="'main-'+ mainCur" @scroll="noScroll">
					<!-- 右边 -->
					<view class="flex-sub bg-red">
						<view v-for="(item, index) in rightNav" :key="index" :id="'main-'+ index">
							<view style="height: 260upx; background-color: #023C41;">
								<view>
									<text>{{item.name}}</text>
								</view>
								<view class="flex justify-end text-center text-content">
									<view class="item bg-blue"  
									:class="rightListSum[index].delAnimation ? 'delRightShoppingCardAnimations' : 'delLeftShoppingCardAnimations'" 
					 v-if="rightListSum[index].showDel" @click="delShoppingCard(index)">
										<text>-</text>
									</view>
									<view style="width: 60upx;" v-if="rightListSum[index].showDel">
										<text>{{rightListSum[index].sum}}</text>
									</view>
									<view class="item bg-blue" :data-index="index" @click="addShoppingCard">
										<text>+</text>
									</view>
								</view>
							</view>
							<view class="gray_big_line"></view>
						</view>
					</view>
				</scroll-view>
				
				<!-- 小球动画 -->
				<view :animation="animationY" :style="'position:fixed;top:' + ballY + 'px;'" v-if="showBall">
					<view class="ball" :animation="animationX" :style="'position:fixed;left:' + ballX + 'px;'"></view>
				</view>
				
			</view>
			
			<!-- 底部导航栏 -->
			<view style="position: fixed; bottom: 0; height: 85upx; width: 100%; background-color: #CCCCCC;">
				<view v-if="showShoppingCart" :class="shoppingCartAnimation? 'shoppingCartAnimations' : '' " style="margin-left: 24upx;width: 85upx;height: 85upx; border-radius: 100%;background-color: red;">
					
				</view>
			</view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				rightListSum: [
					{
						showDel: false,
						delAnimation: false, // 删除动画
						sum: 0,
					}
				],
				showShoppingCart: true, // 购物车显示
				shoppingCartAnimation: false, // 购物车动画
				showBall: false, // 小球是否显示
				animationY: '', // 动画Y
				animationX: '', // 动画X
				ballY: '', // 小球当前位置Y
				ballX: '', // 小球当前位置X
				leftNav: [
					{
						name: 'leftNav菜单1',
						id: 0
					},
					{
						name: 'leftNav菜单2',
						id: 1
					},
					{
						name: 'leftNav菜单3',
						id: 2
					},
					{
						name: 'leftNav菜单4',
						id: 3
					},
					{
						name: 'leftNav菜单5',
						id: 4
					}
				],
				rightNav: [
					{
						name: 'rightNav菜单1',
						id: 0
					},
					{
						name: 'rightNav菜单2',
						id: 1
					},
					{
						name: 'rightNav菜单3',
						id: 2
					},
					{
						name: 'rightNav菜单4',
						id: 3
					},
					{
						name: 'rightNav菜单5',
						id: 4
					},
					{
						name: 'rightNav菜单6',
						id: 5
					},
					{
						name: 'rightNav菜单7',
						id: 6
					}
				],
				curListIndex: 0,
				mainCur: 0,
				listHeight: [], // 左边列表高度
				scrollY: ''
			}
		},
		onLoad(option) {
			console.log('onLoad')
			this.calculateHeight()
		},
		onReady() {
			// 接口返回
			// this.calculateHeight()
		},
		onShow() {
		},
		methods: {
			// 删除购物车
			async delShoppingCard(index) {
				console.log(index)
				if (this.rightListSum[index].sum-1 <= 0) {
					this.rightListSum[index].sum = this.rightListSum[index].sum - 1
					
					this.rightListSum[index].delAnimation = false
					
					this.setDelayTime(100).then(() => {
						this.rightListSum[index].showDel = false
					})
					
				} else {
					this.rightListSum[index].sum = this.rightListSum[index].sum - 1
				}
			},
			// 添加购物车
			addShoppingCard(e){
				console.log(e)
				let index = e.currentTarget.dataset.index
				this.rightListSum[index].sum = this.rightListSum[index].sum + 1
				this.rightListSum[index].delAnimation = true
				this.rightListSum[index].showDel = true
				// x, y表示手指点击横纵坐标, 即小球的起始坐标
				let ballX = e.detail.x,
					ballY = e.detail.y;
				this.createAnimation(ballX, ballY);
			},
			// 延迟执行
			setDelayTime(sec) {
				return new Promise((resolve, reject) => {
					setTimeout(() => {resolve()}, sec)
				});
			},
			// 创建动画
			createAnimation(ballX, ballY) {
				uni.getSystemInfo({
					success: async (e) => {
						// var bottomX = e.windowWidth; // 结束位置X
						var bottomX = 50; // 结束位置X
						var bottomY = e.windowHeight - 50; // 结束位置Y
						var animationX = this.flyX(bottomX, ballX); // 创建小球水平动画
						var animationY = this.flyY(bottomY, ballY); // 创建小球垂直动画
						this.ballX = ballX; // 小球当前位置X
						this.ballY = ballY; // 小球当前位置Y
						this.showBall = true; // 显示小球
						
						this.setDelayTime(100).then(() => {
							this.animationX = animationX.export(); // 执行动画X
							this.animationY = animationY.export(); // 执行动画Y
							// 400ms延时, 即小球的抛物线时长
							return this.setDelayTime(400);
						}).then(() => {
							this.animationX = this.flyX(0, 0, 0).export(); // 执行动画X
							this.animationY = this.flyY(0, 0, 0).export(); // 执行动画Y
							this.showBall = false; // 隐藏小球
							this.shoppingCartAnimation =  true // 购物车动画
							// 400ms延时, 即小球的抛物线时长
							return this.setDelayTime(400);
						}).then(() => {
							this.shoppingCartAnimation =  false // 购物车动画
						})
						
					}
				})
			},
			// 水平动画
			flyX(bottomX, ballX, duration) {
				/**
				 * bottomX 结束位置
				 * ballX 开始位置
				 * duration 动画持续时间
				 */
				let animation = uni.createAnimation({
					duration: duration || 400,
					timingFunction: 'linear',
				})
				animation.translateX(bottomX-ballX).step(); // 动画效果
				return animation;
			},
			// 垂直动画
			flyY(bottomY, ballY, duration) {
				/**
				 * bottomY 结束位置
				 * ballY 开始位置
				 * duration 动画持续时间
				 */
				let animation = uni.createAnimation({
					duration: duration || 400,
					timingFunction: 'ease-in',
				})
				console.log(bottomY)
				animation.translateY(bottomY-ballY).step(); // 动画效果
				return animation;
			},
			// 滑动
			noScroll(e) {
				// console.log(e)
				console.log(e.detail.scrollTop)
				this.scrollY = e.detail.scrollTop + 20
				// 当滚动到顶部
				if (this.scrollY < 0) {
					this.curListIndex = 0
					// this.mainCur = 0
					return true
				}
				// 在中间部分滚动
				for (let i = 0; i < this.listHeight.length - 1; i++) {
					let height = this.listHeight[i]
					// 思路 拿数组里面的开始、结束 值进行范围比较
					if (this.scrollY > height.start && this.scrollY < height.end) {
						console.log('-----------合格----------')
						console.log(this.listHeight[i])
						this.curListIndex = i
						// this.mainCur = i
					}
				}
			},
			 // 左边导航栏点击
			goAnchor(e) {
				console.log(e)
				console.log(e.currentTarget.dataset.id)
				this.curListIndex = e.currentTarget.dataset.id // 下标选中
				this.mainCur = e.currentTarget.dataset.id // 右边锚点
				console.log(this.mainCur)
			},
			// 计算每个左边区块的高度
			calculateHeight() {
				let list = this.rightNav
				let tabHeight = 0;
				for (let i = 0; i < list.length; i++) {
					const query = uni.createSelectorQuery().in(this);
					// console.log(query)
					query.select("#main-" + i).boundingClientRect(data => {
					  // console.log(data);
					  // console.log(data.height);
					  let res = {}
					  res.start = tabHeight // 开始高度
					  tabHeight = tabHeight + data.height;
					  res.end = tabHeight // 结束高度
					  this.listHeight.push(res)
					}).exec();
					
					let rightListSum = {
						showDel: false,
						delAnimation: false, // 删除动画
						sum: 0,
					}
					this.rightListSum.push(rightListSum)
				}
				console.log(this.listHeight)
				console.log(this.rightListSum)
				// let view = uni.createSelectorQuery().select("#main-" + 5);
				// console.log(view)
				// view.fields({
				// 	size: true
				// }, data => {
				// 	console.log(data)
				// }).exec();
				
			}
		}
	}
</script>
