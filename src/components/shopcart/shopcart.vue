<template>
	<div class="shopcart"> 
		<div class="content" >
			<div class="content-left" @click="toggleList">
				<div class="logo-wrapper">
					<div class="logo" :class="{'highlight':totalCount>0}">
						<svg class="icon" :class="{'highlight':totalCount>0}" aria-hidden="true">
						  <use xlink:href="#icon-shopping_cart"></use>
						</svg>
					</div>
					<div class="badge" v-show="totalCount>0">{{totalCount}}</div>
				</div>
				
				<div class="price" :class="{'highlight':totalPrice>0}" >¥{{totalPrice}}</div>
				<div class="desc">		另需配送费¥{{deliveryPrice}}元</div>
			</div>
			<div class="content-right">
				<div class="pay" :class="payClass" @click="pay">{{payDesc}}
				</div>
			</div>
			<div class="ball-container">
				<div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
					<div class="inner inner-hock"></div>
				</div>
			</div>
			<div class="shopcart-list" transition="fold" v-show="listShow">
				<div class="list-header">
					<h1 class="title">购物车</h1>
					<span class="empty"  @click="empty">清空</span>
				</div>
				<div class="list-content" v-el:list-content>
					<ul>
						<li class="food border-1px" v-for="food in selectFoods">
							<span class="name">{{food.name}}</span>
							<div class="price">
								<span>¥{{food.count*food.price}}</span>
							</div>
							<div class="cartcontrol-wrapper">
								<cartcontrol :food="food"></cartcontrol>
							</div>
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
	<div class="list-mask" @click="hideList" transition="fade" v-show="listShow"></div>
</template>
<script type="text/esmascript-6">
	import cartcontrol from 'components/cartcontrol/cartcontrol';
	import BSCroll from 'better-scroll';
	export default {
		props: {
			selectFoods: {
				type: Array,
				default() {
					return [];
				}
			},
			deliveryPrice: {
				type: Number,
				default: 0
			},
			minPrice: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				balls: [
				{
					show: false
				},
				{
					show: false
				},
				{
					show: false
				},
				{
					show: false
				},
				{
					show: false
				}
				],
				dropBalls: [],
				fold: true
			};
		},
		computed: {
			totalPrice() {
				let total = 0;
				this.selectFoods.forEach((food) => {
					total += food.price *　food.count;
				});
				return total;
			},
			totalCount() {
				let count = 0;
				this.selectFoods.forEach((food) => {
					count += food.count;
				});
				return count;
			},
			payDesc() {
				if(this.totalPrice === 0){
					return `¥${this.minPrice}元起送`;
				}else if(this.totalPrice < this.minPrice){
					let diff = this.minPrice - this.totalPrice;
					return `还差¥${diff}元起送`;
				}else{
					return '去结算';
				}
			},
			payClass() {
				if(this.totalPrice < this.minPrice){
					return 'not-enough';
				}else{
				   return 'enough';
				}
			},
			listShow() {
				if (!this.totalCount) {
					this.fold = true;
					return false;
				}
				let show = !this.fold;
				if (show) {
					this.$nextTick(() => {
						if (!this.scroll) {
							this.scroll = new BSCroll(this.$els.listContent, {
								click:true
							});
						} else {
							this.scroll.refresh();
						}
					});
				}
				return show;
			}
		},
		methods: {
			pay() {
				if(this.totalPrice < this.minPrice){
					return;
				}
				alert("跳转支付页面");
			},
			drop(el) {
				for (let i = 0; i < this.balls.length; i++) {
					let ball = this.balls[i];
					if (!ball.show){
						ball.show = true;
						ball.el = el;
						this.dropBalls.push(ball);
						return;
					}
				}
			},
			toggleList() {
				if (!this.totalCount) {
					return false;
				}
				this.fold = !this.fold;
			},
			hideList() {
				this.fold = true;
			},
			empty() {
				this.selectFoods.forEach((food) => {
					food.count = 0;
				});
			}
		},
		transitions: {
				drop: {
					beforeEnter(el) {
							let count = this.balls.length;
							while (count--) {
								let ball = this.balls[count];
								if (ball.show) {
									let rect = ball.el.getBoundingClientRect();
									// console.log(rect);
									let x = rect.left - 32;
									let y = -(window.innerHeight - rect.top - 44);
									el.style.display = '';
									el.style.webkitTransform = `translate3d(0,${y}px,0)`;
									el.style.transform = `translate3d(0,${y}px,0)`;
									let inner = el.getElementsByClassName('inner-hock')[0];
									inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
									inner.style.transform = `translate3d(${x}px,0,0)`;
								}
							}
					},
					enter(el) {
							/* eslint-disable no-unused-vars */
							let rf = el.offsetHeight;
							this.$nextTick(() => {
									el.style.webkitTransform = 'translate3d(0,0,0)';
									el.style.transform = 'translate3d(0,0,0)';	
									let inner = el.getElementsByClassName('inner-hock')[0];
									inner.style.webkitTransform = 'translate3d(0,0,0)';
									inner.style.transform = 'translate3d(0,0,0)';
							});
					},
					afterEnter(el) {
						let ball = this.dropBalls.shift();
						if (ball) {
							ball.show = false;
							el.style.display = 'none';
						}
					}
				}
		},
		components: {
				cartcontrol
		}
	}
</script>
<style lang="scss" rel="stylesheet/scss">
@import '../../common/sass/common';
@import '../../common/sass/minxin';
	.shopcart{
		position: fixed;
		left:0;
		bottom:0;
		z-index:101;
		width:100%;
		height:1.28rem;
		.content{
			display: flex;
			background: #141d27;
			font-size:0;
			color:rgba(255,255,255,.4);
			.content-left{
				flex:1;
				.logo-wrapper{
					display:inline-block;
					position:relative;
					top: -0.266667rem;
					margin:0 0.32rem;
					padding:0.16rem;
					width:1.493333rem;
					height:1.493333rem;
					line-height:1.493333rem;
					text-align:center;
					box-sizing: border-box;
					vertical-align:top;
					border-radius: 50%;
					background:#141d27;
					.logo{
						width:100%;
						height:100%;
						border-radius:50%;
						background:#2b343c;
						&.highlight{
							background: rgb(0,160,220);
						}
						.icon{
							line-height:1.173333rem;
							font-size:0.64rem;
							color:#80858a;
							&.highlight{
								color:#fff;
							}
						}
					}
				}
				.price{
					display:inline-block;
					vertical-align:top;
					line-height:0.64rem;
					margin-top:0.32rem;
					box-sizing:border-box;
					border-right:1px solid rgba(255,255,255,.1);
					font-size:0.426667rem;
					font-weight:700;
					padding-right:0.32rem;
					&.highlight{
						color:#fff;
					}
				}
				.desc{
					display:inline-block;
					vertical-align:top;
					line-height:0.64rem;
					margin:0.32rem 0 0 0.32rem;
					font-size:0.266667rem;
				}
			}
			.content-right{
				flex:0 0 2.8rem;
				width:2.8rem;
				.pay{
					height:1.28rem;
					line-height:1.28rem;
					text-align:center;
					font-size:0.32rem;
					font-weight:700;
					background-color: #2b333b;
					&.not-enough{
						background: #2b333b;
					}
					&.enough{
						background:#00b43c;
						color:#fff;
					}
				}
			}
		}
		.ball-container{
			.ball{
				position:fixed;
				left:0.853333rem;
				bottom:0.586667rem;
				z-index:200;
				&.drop-transition{
					transition: all .4s cubic-bezier(0.49,-0.29,0.75,0.41);
					.inner{
						width:0.426667rem;
						height:0.426667rem;
						border-radius:50%;
						background:rgb(0,160,220);
						transition:all .4s linear;
					}
				}
			}
		}
		.shopcart-list{
			position: absolute;
			top:0;
			left:0;
			z-index:-1;
			width:100%;
			&.fold-transition{
				transition:all .5s;
				transform:translate3d(0,-100%,0);
			}
			&.fold-enter,&.fold-leave{
				transform:translate3d(0,0,0);
			}
			.list-header{
				height:1.066667rem;
				line-height:1.066667rem;
				padding: 0 0.48rem;
				background:#f3f5f7;
				border-bottom:1px solid rgba(7,17,27,.1);
				.title{
							float:left;
							font-size:0.373333rem;
							color:rgb(7,17,27);
				}
				.empty{
					float:right;
					color:rgb(0,160,220);
					font-size:0.32rem;
				}
			}
			.list-content{
				padding:0 0.48rem;
				max-height:5.786667rem;
				background:#fff;
				overflow:hidden;
				.food{
					position:relative;
					padding: 0.32rem 0;
					box-sizing: border-box;
					@include border-1px-tb(bottom,rgba(7,17,27,.1))
					.name{
						line-height:0.64rem;
						font-size:0.373333rem;
						color:rgb(7,17,27);
					}
					.price{
						position:absolute;
						right:2.4rem;
						bottom:0.32rem;
						font-size:0.373333rem;
						line-height:0.64rem;
						font-weight:700;
						color:rgb(240,20,20);
					}
					.cartcontrol-wrapper{
						position:absolute;
						right:0;
						bottom:0.16rem;
					}
				}
			}
		}
	}
	.list-mask{
		position:fixed;
		top:0;
		left:0;
		width:100%;
		height:100%;
		z-index:40;
		backdrop-filter:blur(10px);
		transition:all .4s;
		&.fade-transition{
			opacity:1;
			background:rgba(7,17,27,.6);
		}
		&.fade-enter,&.fade-leave{
			opacity:0;
			background:rgba(7,17,27,0);
		}
	}
</style>