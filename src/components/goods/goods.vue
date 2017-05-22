<template>
	<div class="goods">
		<div class="menu-wrapper" v-el:menu-wrapper>
			<ul>
				<li v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}" @click="selectMenu($index,$event)">
					<span class="text border-1px">
						<span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
						{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" v-el:foods-wrapper>
			<ul>
				<li v-for="item in goods" class="food-list food-list-hook">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li @click.stop.prevent="selectFood(food,$event)" v-for="food in item.foods" class="food-item border-1px">
							<div class="icon">
								<img :src="food.icon" >
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span>月售{{food.sellCount}}份</span>
									<span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span>¥{{food.price}}</span>
									<span v-show="food.oldPrice" class="old-price">¥{{food.oldPrice}}</span>
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food"></cartcontrol>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
	</div>
	<food :food="selectedFood" v-ref:food></food>
</template>

<script type="text/ecmascript-6">
	import BSCroll from 'better-scroll';
	import shopcart from 'components/shopcart/shopcart';
	import cartcontrol from 'components/cartcontrol/cartcontrol';
	import food from 'components/food/food';
	const ERR_OK = 0;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				goods: [],
				listHeight: [],
				scrollY: 0,
				selectedFood: {}
			};
		},
		computed: {
			currentIndex() {
				for (let i = 0; i < this.listHeight.length; i++) {
					let height1 = this.listHeight[i];
					let height2 = this.listHeight[i + 1];
					if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
						 return i;
					}
				}
				return 0;
			},
			selectFoods() {
				let foods = [];
				this.goods.forEach((good) => {
						good.foods.forEach((food) => {
							if (food.count) {
								foods.push(food);
							}
						});
				});
				return foods;
			}
		},
		created() {
			this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
			this.$http.get('/api/goods').then((response) => {
				response = response.body;
				if (response.errno === ERR_OK) {
					this.goods = response.data;
					this.$nextTick(() => {
						this._initScroll();
						this._calculateHeight();
					});
				}
			});
		},
		methods: {
			_initScroll() {
				this.menuScroll = new BSCroll(this.$els.menuWrapper, {
					click: true
				});
				this.foodsScroll = new BSCroll(this.$els.foodsWrapper, {
					click: true,
					probeType: 3
				});
				this.foodsScroll.on('scroll', (pos) => {
					this.scrollY = Math.abs(Math.round(pos.y));
				});
			},
			_calculateHeight() {
					let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
					let height = 0;
					this.listHeight.push(height);
					for (let i = 0; i < foodList.length; i++) {
						let item = foodList[i];
						height += item.clientHeight;
						this.listHeight.push(height);
					}
			},
			selectMenu(index, event) {
				if (!event._constructed) {
					return;
				}
				let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
				let el = foodList[index];
				this.foodsScroll.scrollToElement(el, 300);
			},
			selectFood(food, event) {
				if (!event._constructed) {
					return;
				}
				this.selectedFood = food;
				this.$refs.food.show();
			},
			_drop(target) {
				// 体验优化，异步执行下落动画
				this.$nextTick(() => {
					this.$refs.shopcart.drop(target);
				});
			}
		},
		components: {
			shopcart,
			cartcontrol,
			food
		},
		events: {
			'cart.add'(target) {
				this._drop(target);
			}
		}
	};
</script>

<style lang="scss" rel="stylesheet/scss">
 @import '../../common/sass/minxin';
	.goods{
		display:flex;
		position:absolute;
		top:4.64rem;
		bottom:1.226667rem;
		width:100%;
		overflow:hidden;
		.menu-wrapper{
			flex:0 0 2.133333rem;
			width:2.133333rem;//兼容安卓
			background-color: #f3f5f7;
			.menu-item{
				display:table;
				line-height:0.373333rem;
				height:1.44rem;
				width:1.493333rem;
				padding:0 0.32rem;
				text-align:center;
				&.current{
					position:relative;
					z-index:10;
					background-color:#fff;
					font-weight:700;
					margin-top:-1px;
				}
				.icon{
					display:inline-block;
					width:0.32rem;
					height:0.33rem;
					vertical-align:top;
					margin-right:0.053333rem;
					background-size:cover;
					background-repeat:no-repeat;
					&.decrease{
						@include bg-image('decrease_3');
					}
					&.discount{
						@include bg-image('discount_3');
					}
					&.guarantee{
						@include bg-image('guarantee_3');
					}
					&.invoice{
						@include bg-image('invoice_3');
					}
					&.special{
						@include bg-image('special_3');
					}
				}
				.text{
					font-size:0.32rem;
					display:table-cell;
					width:1.493333rem;
					vertical-align:middle;
					@include border-1px-tb(bottom,rgba(7,17,27,.2));
				}
			}
		}
		.foods-wrapper{
			flex: 1;
			.title{
				padding-left:0.373333rem;
				height:0.693333rem;
				line-height:0.693333rem;
				border-left:0.053333rem solid #d9dde1;
				font-size:0.32rem;
				color:rgb(147,153,159);
				background-color:#f3f5f7;
			}
			.food-item{
				display: flex;
				margin: 0.48rem;
				padding-bottom:0.48rem;
				@include border-1px-tb(bottom,rgba(7,17,27,.2));
				&:last-child:after{
					border:none;
				}
				&:last-child{
					margin-bottom:0;
				}
				.icon{
					width:1.52rem;
					height:1.52rem;
					flex: 0 0 1.52rem;
					margin-right:0.266667rem;
					img{
						width:100%;
						height:100%;
					}
					
				}
				.content{
					flex:1;
					.name{
						margin: 0.053333rem 0  0.213333rem 0;
						font-size:0.373333rem;
						line-height:0.373333rem;
						color:rgb(7,17,27);
					}
					.desc, .extra{
						font-size:0.266667rem;
						color:rgb(147,153,159);
						line-height:0.266667rem;
						margin-bottom:0.213333rem;
					}
					.extra{
						margin-bottom:0;
						span{
							padding-right: 0.32rem;
						}
					}
					.price{
						span{
							font-size:0.373333rem;
							line-height:0.373333rem;
							color:rgb(240,20,20);
							font-weight: normal;
							line-height:0.64rem;
							margin-right:0.213333rem;
						}	
						.old-price{
							color:rgb(147,153,159);
							text-decoration: line-through;
						}
					}
					.cartcontrol-wrapper{
						position:absolute;
						right: 0;
						bottom: 0.32rem
					}
				}
			}
		}
	}
</style>