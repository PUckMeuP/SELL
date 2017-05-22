<template>
	<div class="food" v-show="showFlag" transition="move" v-el:food>
		<div class="food-content">
			<div class="image-header">
				<img :src="food.image">
				<div class="back" @click="hide">
					<svg class="icon icon-arrow_lift1" :class="{'highlight':totalCount>0}" aria-hidden="true">
			  			<use xlink:href="#icon-arrow_lift1"></use>
					</svg>
				</div>
			</div>
			<div class="content">
						<h1 class="title">{{food.name}}</h1>
						<div class="detail">
							<span class="sell-count">月售{{food.sellCount}}</span>
							<span class="rating">好评率{{food.rating}}</span>
						</div>
						<div class="price">
							<span class="now">¥{{food.price}}</span>
			    <span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
			</div>
			<div class="cartcontrol-wrapper">
				<cartcontrol :food="food"></cartcontrol>	
			</div>
			<div class="buy" transition="fade1" @click.stop.prevent="addFirst($event)" v-show="!food.count || food.count === 0">加入购物车
			</div>
		</div>	
			<split v-show="food.info"></split>	
			<div class="info" v-show="food.info">
				<h1 class="title">商品信息</h1>
				<p class="text">{{food.info}}</p>
			</div>	
			<split></split>	
			<div class="rating">
				<h1 class="title">商品评价</h1>
			</div>
			<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
			<div class="rating-wrapper">
				<ul v-show="food.ratings && food.ratings.length">
					<li v-for="rating in food.ratings" class="rating-item border-1px" v-show="needShow(rating.rateType,rating.text)">
					<div class="user">	
						<span class="name">{{rating.username}}</span>
						<img  class="avater" :src="rating.avatar" >
					</div>
					<div class="time">	
							{{rating.rateTime | formatDate}}
					</div>
					<p class="text">
						<span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
					</p>
					</li>
				</ul>
				<div class="no-rating" v-show="!food.ratings ||!food.ratings.length">
					暂无评价！
				</div>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import {formatDate} from 'common/js/date';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import ratingselect from 'components/ratingselect/ratingselect';
  import split from 'components/split/split';
  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        }
        this.$dispatch('cart.add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      }
    },
    filters: {
    	formatDate(time) {
    		let date = new Date(time);
    		return formatDate(date, 'yyyy-MM--dd hh:mm');
    	}
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    components: {
      cartcontrol,
      ratingselect,
      split
    }
  };
</script>

<style lang="scss" type="stylesheet/scss">
@import "../../common/sass/minxin";
	.food{
		position: fixed;
		left:0;
		top:0;
		bottom:1.28rem;
		z-index:30;
		width:100%;
		background:#fff;
		&.move-transition{
			transition:all .2s;
			transform:translate3d(0,0,0);
		}
		&.move-enter,&.move-leave{
			transition:all .2s;
			transform:translate3d(-100%,0,0);
		}
		.image-header{
			position:relative;
			width:100%;
			height:0;
			padding-top:100%;
			img{
				position:absolute;
				top:0;
				left:0;
				width:100%;
				height:100%;
			}
			.back{
				position:absolute;
				top:10px;
				left:0;
				.icon-arrow_lift1{
					display:block;
					padding:0.266667rem;
					font-size:0.533333rem;
					color:#fff!important;
				}
			}
		}
		.content{
			position:relative;
			padding:0.48rem;
			.title{
				line-height:0.373333rem;
				margin-bottom:0.213333rem;
				font-size:0.373333rem;
				font-weight:700;
				color:rgb(7,17,27);
			}
			.detail{
				margin-bottom:0.48rem;
				line-height:0.266667rem;
				font-size:0;
				height:0.266667rem;
				.sell-count,.rating{
					font-size:0.266667rem;
					color:rbg(147,153,159);
				}
				.sell-count{
					margin-right:0.32rem;
				}
			}
			.price{
				font-weight:700;
				line-height:0.64rem;
				.now{
					margin-right:0.213333rem;
					font-size:0.373333rem;
					color:rgb(240,20,20);
				}
				.old{
					text-decoration:line-through;
					font-size:0.266667rem;
					color:rbg(147,153,159);
				}
			}
		}
		.cartcontrol-wrapper{
			position:absolute;
			right:0.32rem;
			bottom:0.32rem;
		}
		.buy{
			position:absolute;
			right:0.32rem;
			bottom:0.32rem;
			z-index:10;
			height:0.64rem;
			line-height:0.64rem;
			padding:0 0.32rem;
			font-size:0.266667rem;
			border-radius:0.32rem;
			color:#fff;
			background:rgb(0,160,220);
			&.fade1-transition{
				transition: all .2s;
				opacity: 1;
			}
			&.fade1-enter, &.fade1-leave{
				opacity:0;
				z-index:-1;
			}
		}
		.info{
			padding:0.48rem;
			.title{
				line-height:0.373333rem;
				margin-bottom:0.16rem;
				font-size:0.373333rem;
				color:rgb(7,17,27);
			}
			.text{
				line-height:0.64rem;
				padding:0 0.213333rem;
				font-size:0.32rem;
				color:rgb(77,85,93);
			}
		}
		.rating{
			padding-top:0.48rem;
			.title{
				line-height:0.373333rem;
				margin-left:0.48rem;
				font-size:0.373333rem;
				color:rgb(7,17,27);
			}
		}
		.rating-wrapper{
					padding:0 0.48rem;
					.rating-item{
						position:relative;
						padding:0.426667rem 0;
						@include border-1px-tb(bottom,rgba(7,17,27,.1));
						.user{
							position:absolute;
							right:0;
							top:0.426667rem;
							font-size:0;
							line-height:0.32rem;
							.name{
								margin-left:0.32rem;
								display:inline-block;
								vertical-align:top;
								font-size:0.266667rem;
								color:rgb(147,153,159);
							}
							.avater{
								border-radius:50%;
								width:0.32rem;
								height:0.32rem;
								margin-left:0.16rem;
							}
						}
						.time{
							line-height:0.32rem;
							font-size:0.266667rem;
							color:rgb(147,153,159);
							margin-bottom:0.16rem;
						}
						.text{
							line-height:0.426667rem;
							font-size:0.32rem;
							color:rgb(7,17,27);
							.icon-thumb_up,.icon-thumb_down{
								margin-right:0.106667rem;
								line-height:0.32rem;
								font-size:0.32rem;
							}
							.icon-thumb_up{
								color:rgb(0,160,220);
							}
							.icon-thumb_down{
								color:rgb(147,153,159);
							}
						}
					}
					.no-rating{
						padding:0.426667rem;
						font-size:0.32rem;
						color:rgb(147,153,159);
					}
				}
	}
</style>