<template>
	<div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img class="img-size" :src="seller.avatar">
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">
					{{seller.description}}/{{seller.deliveryTime}}分钟到达
				</div>
				<div v-if="seller.supports" class="support">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div v-if="seller.supports" class="support-count" @click="showDetail">
				<span class="count">{{seller.supports.length}}个</span>
				<svg class="icon" aria-hidden="true">
				  <use xlink:href="#icon-keyboard_arrow_right"></use>
				</svg>
			</div>
		</div>
		<div class="bulletin-wrapper" @click="showDetail">
			<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}
				<svg class="icon" aria-hidden="true">
				  <use xlink:href="#icon-keyboard_arrow_right"></use>
				</svg>
			</span>	
		</div>
		<div class="background">
			<img :src="seller.avatar" width="100%" height="100%">
		</div>
		<div v-show="detailShow" class="detail" transition="fade">
			<div class="detail-wrapper clearfix">
				<div class="detail-main">
					<h1 class="name">{{seller.name}}</h1>
					<star :size="48" :score="seller.score"></star>
					<div class="title">
						<div class="line"></div>
						<div class="text">优惠信息</div>
						<div class="line"></div>
					</div>
					<ul v-if="seller.supports" class="supports">
						<li class="support-item" v-for="item in seller.supports">
							<span class="icon" :class="classMap[seller.supports[$index].type]"></span>
							<span class="text">{{seller.supports[$index].description}}</span>
						</li>
					</ul>
					<div class="title">
						<div class="line"></div>
						<div class="text">商家公告</div>
						<div class="line"></div>
					</div>
					<div class="bulletin">
						<p class="content">{{seller.bulletin}}</p>
					</div>
				</div>
			</div>
			<div class="detail-close" @click="hideDetail">
				<svg class="icon" aria-hidden="true">
				  <use xlink:href="#icon-close"></use>
				</svg>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
    import star from 'components/star/star';
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				detailShow: false
			};
		},
		methods: {
			showDetail() {
				this.detailShow = true;
			},
			hideDetail() {
				this.detailShow = false;
			}
		},
		created() {
			this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
		},
		components: {
			star
		}
	};
</script>

<style lang="scss" rel="stylesheet/scss">
	@import '../../common/sass/minxin';

	.header{
		color:#fff;
		position:relative;
		background:rgba(7,17,27,.5);
		overflow:hidden;
		.content-wrapper{
			padding: 0.64rem 0.32rem 0.48rem 0.64rem;
			font-size:0;
			position: relative;
			.avatar{
				display:inline-block;
				vertical-align:top;
				border-radius:0.053333rem;
				overflow: hidden;
				.img-size{
					width:1.706667rem;
					height:1.706667rem;
				}
			}
			.content{
				font-size:0.373333rem;
				display:inline-block;
				margin-left:0.426667rem;
				.title{
					margin: 0.053333rem 0 0.106667rem 0;
					.brand{
						width:0.8rem;
						height:0.48rem;
						vertical-align: middle;
						display:inline-block;
						@include bg-image("brand");
						background-size:.8rem .48rem ;
						background-repeat: no-repeat;
						.name{
							margin-left:0.106667rem;
							font-size:0.426667rem;
							line-height:0.426667rem;
						}
						
					}
				}
				.description{
					margin-bottom:0.213333rem;
					margin-top:0.213333rem;
					line-height:0.32rem;
					font-size:0.32rem;
				}
				.support{
					.icon{
						display:inline-block;
						vertical-align:middle;
						width:0.32rem;
						height:0.32rem;
						margin-right:0.106667rem;
						background-size:0.32rem 0.32rem;
						background-repeat:no-repeat;
						&.decrease{
							@include bg-image('decrease_1');
						}
						&.discount{
							@include bg-image('discount_1');
						}
						&.guarantee{
							@include bg-image('guarantee_1');
						}
						&.invoice{
							@include bg-image('invoice_1');
						}
						&.special{
							@include bg-image('special_1');
						}
					}
					.text{
						font-size:0.266667rem;
						line-height:0.266667rem;
					}
				}

			}
			.support-count{
				position:absolute;
				right:0.32rem;
				bottom:0.4rem;
				height:0.6rem;
				padding: 0 0.213333rem;
				font-size:0.266667rem;
				line-height:0.64rem;
				background-color:rgba(0,0,0,.2);
				border-radius:0.373333rem;
				.count{
					vertical-align:top;
				}
			}
		}
		.bulletin-wrapper{
			height:0.746667rem;
			line-height:0.746667rem;
			padding:0 0.586667rem 0 0.32rem;
			white-space:nowrap;
			overflow:hidden;
			text-overflow: ellipsis;
			position:relative;
			background-color:rgba(7,17,17,.2);
			.bulletin-title{
				display:inline-block;
				width:0.586667rem;
				height:0.32rem;
				@include bg-image('bulletin');
				background-size:0.586667rem 0.32rem;
				background-repeat:no-repeat;
				vertical-align:middle;
				margin-right:0.106667rem;
			}
			.bulletin-text{
				font-size:0.266667rem;
				.icon{
					position:absolute;
					top:0.226667rem;
					right:0.32rem;
					vertical-align:top;
				}
			}
		}
		.background{
			position:absolute;
			left:0;
			top:0;
			width:100%;
			height:100%;
			z-index:-1;
			filter:blur(10px);
		}
		.detail{
			position:fixed;
			width:100%;
			height:100%;
			top:0;
			left:0;
			z-index:10000;
			overflow:auto;
			background-color:rgba(7,17,27,.8);
			transition:all .5s;
			backfrop-filter:blur(10px); 
			&.fade-transition{
				opacity: 1;
				background:rgba(7,17,27,.8);
			}
			&.fade-enter,&.fade-leave{
				opacity:0;
				background:rgba(7,17,27,0);
			}
			.detail-wrapper{
				min-height: 100%;
				width:100%;
				.detail-main{
					margin-top:1.706667rem;
					padding-bottom:1.7rem;
					.name{
						font-size:0.426667rem;
						font-weight:700;
						color:#fff;
						line-height:0.426667rem;
						text-align:center;
					}
					.star{
							margin-top:0.48rem;
							padding:0.053333rem 0;
							text-align:center;
					}
					.title{
							display:flex;
							width:80%;
							margin: 0.746667rem auto 0.64rem auto;
							.line{
								position:relative;
								flex:1;
								top:-0.16rem;
								border-bottom:1px solid rgba(255,255,255,.2);
							}
							.text{
								padding:0 0.32rem;
								font-weight:700;
								font-size:0.373333rem;
								color:#fff;
							}
						}
					.supports{
						width:80%;
						margin:0 auto;
						.support-item{
							padding:0 0.32rem;
							margin-bottom: 0.32rem;
							font-size:0;
							&:last-child{
								margin-bottom:0;
							}
							.icon{
								display:inline-block;
								width:0.426667rem;
								height:0.426667rem;
								vertical-align:top;
								margin-right:0.16rem;
								background-size:0.426667rem 0.426667rem;
								background-repeat:no-repeat;
								&.decrease{
									@include bg-image('decrease_2');
								}
								&.discount{
									@include bg-image('discount_2');
								}
								&.guarantee{
									@include bg-image('guarantee_2');
								}
								&.invoice{
									@include bg-image('invoice_2');
								}
								&.special{
									@include bg-image('special_2');
								}
							}
							.text{
								line-height:0.426667rem;
								font-size:0.32rem;
							}
						}
					}
					.bulletin{
						width:80%;
						margin:0 auto;
						.content{
							font-size:0.32rem;
							padding: 0 0.16rem;
							line-height: 0.64rem;
						}
					}
				}
			}
			.detail-close{
				position:relative;
				width:0.853333rem;
				height:0.853333rem;
				color:rgba(255,255,255,.5);
				margin:-1.7rem auto 0 auto;
				clear:both;
				.icon{
					width:0.853333rem;
					height:0.853333rem;
					font-size:1.706667rem;
				}
			}

		}
	}
	
</style>