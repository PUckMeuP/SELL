<template>
	<div class="seller" v-el:seller>
		<div class="seller-content">
			<div class="overview">
				<h1 class="title">{{seller.name}}</h1>
				<div class="desc border-1px">
					<star :size="36" :score="seller.score"></star>
					<span class="text">({{seller.ratingCount}})</span>
					<span class="text">月售{{seller.sellCount}}单</span>
				</div>
				<ul class="remark">
					<li class="block">
						<h2>起送价</h2>
						<div class="content"><span class="stress">{{seller.minPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2>商机配送</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2>平均配送时间</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryTime}}</span>分钟
						</div>
					</li>
				</ul>
				<div class="favorite" @click="toogleFavotite">
					<span class="icon-favorite" :class="{'active':favorite}"></span>
					<span class="text">{{favoriteText}}</span>
				</div>
			</div>
			<split></split>
			<div class="bulletin">
				<h1 class="title">公告与活动</h1>
				<div class="content-wrapper border-1px">
					<p class="content">{{seller.bulletin}}</p>
					<ul v-if="seller.supports" class="supports">
						<li class="support-item border-1px" v-for="item in seller.supports">
							<span class="icon" :class="classMap[seller.supports[$index].type]"></span>
							<span class="text">{{seller.supports[$index].description}}</span>
						</li>
					</ul>
				</div>
			</div>
			<split></split>
			<div class="pics">
				<h1 class="title">商家实景</h1>
				<div class="pic-wrapper" v-el:pic-wrapper>
					<ul class="pic-list" v-el:pic-list>
						<li class="pic-item" v-for="item in seller.pics">
							<img :src="item" alt="">
						</li>
					</ul>
				</div>
			</div>
			<split></split>
			<div class="info">
				<h1 class="title">商家信息</h1>
				<ul>
					<li class="info-item" v-for="info in seller.infos">{{info}}</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
import BSCroll from 'better-scroll';
import {saveToLocal, LoadFromLocal} from 'common/js/store';
import star from 'components/star/star';
import split from 'components/split/split';
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				favorite: (() => {
					return LoadFromLocal(this.seller.id, 'favorite', false);
				})()
			};
		},
		computed: {
			favoriteText() {
				return this.favorite ? '已收藏' : '收藏';
			}
		},
		created() {
			this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
		},
		watch: {
			'seller'() {
				this._initScroll();
				this._initPics();
			}
		},
		methods: {
			toogleFavotite(event) {
				if (!event._constructed) {
					return;
				}
				this.favorite = !this.favorite;
				saveToLocal(this.seller.id, 'favorite', this.favorite);
			},
			_initScroll() {
				if (!this.scroll) {
					this.scroll = new BSCroll(this.$els.seller, {
						click: true
					});
				} else {
					this.scroll.refresh();
				}
			},
			_initPics() {
					if (this.seller.pics) {
					let picWidth = 240;
					let margin = 16;
					let width = (picWidth + margin) * this.seller.pics.length - margin;
					this.$els.picList.style.width = width + 'px';
					this.$nextTick(() => {
						this.picScroll = new BSCroll(this.$els.picWrapper, {
							scrollX: true,
							eventPassthrough: 'vertical'
						});
					});
				}
			},
			toggleFavorite(event) {
				if (!event._constructed) {
					return;
				}
				this.favorite = !this.favorite;
			}
		},
		ready() {
			this._initScroll();
			this._initPics();
		},
		components: {
			star,
			split
		}
	};
</script>

<style lang="scss" rel="stylesheet/scss">
@import "../../common/sass/minxin";
	.seller{
		position:absolute;
		top:4.606667rem;
		bottom:0;
		width:100%;
		left:0;
		overflow:hidden;
		.overview{
			padding: 0.48rem;
			position:relative;
			.title{
				margin-bottom:0.213333rem;
				line-height:0.373333rem;
				font-size:0.373333rem;
				color:rgb(7,17,27);
			}
			.desc{
					padding-bottom:0.48rem;
					
					@include border-1px-tb(bottom,rgba(7,17,27,.1));
					font-size:0;
					.star{
						display:inline-block;
						vertical-align:top;margin-right:0.213333rem;
					}
					.text{
						display:inline-block;
						line-height:0.48rem;
						font-size:0.32rem;
						vertical-align:top;
						font-size:0.266667rem;
						color:rgb(77,89,93);
						margin-right:0.266667rem;
					}
			}
			.remark{
				display:flex;
				padding-top:0.48rem;
				.block{
					flex:1;
					text-align:center;
					border-right:1px solid rgba(7,17,27,.1);
					&:last-child{
						border:none;
					}
					h2{
						margin-bottom:0.106667rem;
						line-height:0.266667rem;
						font-size:0.266667rem;
						color:rgb(147,153,159);
					}
					.content{
							line-height:0.64rem;
							font-size:0.266667rem;
							color:rgb(7,17,27);
							.stress{
								font-size:0.64rem;
							}
					}
				}
			}
			.favorite{
				position:absolute;
				width:1.333333rem;
				right:0.48rem;
				top:0.48rem;
				text-align:center;
				.icon-favorite{
					display:block;
					color:#d4d6d9;
					line-height:0.64rem;
					font-size:0.64rem;
					&.active{
						color:rgb(240,20,20);
					}
				}
				.text{
					line-height:0.266667rem;
					font-size:0.266667rem;
					color:rgb(77,85,93);
				}
			}
		}
			.bulletin{
					padding:0.48rem 0.48rem 0 0.48rem;
					.title{
						margin-bottom:0.213333rem;
						line-height:0.373333rem;
						color:rgb(7,17,27);
						font-size:0.373333rem;
					}
					.content-wrapper{
						padding:0 0.32rem 0.426667rem 0.32rem;
						@include border-1px-tb(bottom,rgba(7,17,27,.1));
						.content{
							line-height:0.64rem;
							font-size:0.32rem;
							color:rgb(240,20,20);
						}
					}
					.supports{
						.support-item{
							padding:0.426667rem 0.32rem;
							font-size:0;
							@include border-1px-tb(top,rgba(7,17,27,.1));
							&:last-child {
								border:none;
							}
							.icon{
								display:inline-block;
								vertical-align:sub;
								width:0.32rem;
								height:0.32rem;
								margin-right:0.106667rem;
								background-size:0.32rem 0.32rem;
								background-repeat:no-repeat;
								&.decrease{
									@include bg-image('decrease_4');
								}
								&.discount{
									@include bg-image('discount_4');
								}
								&.guarantee{
									@include bg-image('guarantee_4');
								}
								&.invoice{
									@include bg-image('invoice_4');
								}
								&.special{
									@include bg-image('special_4');
								}
							}
							.text{
								line-height:0.426667rem;
								font-size:0.32rem;
								color:rgb(7,17,27);
							}
						}
			}
		}
		.pics{
				padding:0.48rem;
				.title{
					margin-bottom:0.213333rem;
					line-height:0.373333rem;
					color:rgb(7,17,27);
					font-size:0.373333rem;
				}
				.pic-wrapper{
					width:100%;
					overflow:hidden;
					white-space: nowrap;
					.pic-list{
						font-size:0;
						.pic-item{
							margin-right:0.16rem;
							display:inline-block;
							width:3.2rem;
							height:2.4rem;
							img{
								width:3.2rem;
							  height:2.4rem;
							}
							&:last-child{
									margin:0;
								}
						}
					}
				}
				
		}
		.info{
			padding:0.48rem 0.48rem 0 0.48rem;
			.title{
				padding-bottom:0.32rem;
				line-height:0.373333rem;
				@include border-1px-tb(bottom,rgba(7,17,27,.1));
				color: rbg(7,17,27);
				font-size:0.373333rem;
			}
			.info-item{
				padding:0.426667rem  0.32rem;
				line-height:0.426667rem;
				@include border-1px-tb(bottom,rgba(7,17,27,.1));
				font-size:0.32rem;
				&:last-child{
					border:none;
				}
			}
		}
		
	}
</style>