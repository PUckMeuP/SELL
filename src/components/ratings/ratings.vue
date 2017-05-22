<template>
		<div class="ratings" v-el:ratings>
			<div class="ratings-content">
				<div class="overview">
					<div class="overview-left">
						<div class="score">{{seller.score}}</div>
						<div class="title">综合评分</div>
						<div class="rank">高于商家评分{{seller.rankRate}}%</div>
					</div>
					<div class="overview-right">
						<div class="score-wrapper">
							<span class="title">服务态度</span>
							<star :size="36" :score="seller.serviceScore"></star>
							<span class="score">{{seller.foodScore}}</span>
						</div>
						<div class="score-wrapper">
							<span class="title">商品评分</span>
							<star :size="36" :score="seller.foodScore"></star>
							<span class="score">{{seller.foodScore}}</span>
						</div>
						<div >
							<span class="title">送达时间</span>
							<span class="delivery">{{seller.deliveryTime}}分钟</span>
						</div>
					</div>
				</div>
				<split></split>
				<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings"></ratingselect>
				<div class="rating-wrapper">
					<ul>
						<li v-for="rating in ratings" class="rating-item border-1px" v-show="needShow(rating.rateType, rating.text)">
							<div class="avatar">
								<img :src="rating.avatar" alt="">
							</div>
							<div class="content">
								<h1 class="name">{{rating.username}}</h1>
								<div class="star-wrapper">
									<star :size="24" :score="rating.score"></star>
									<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
								</div>
								<p class="text">{{rating.text}}</p>
								<div class="recommend" v-show="rating.recommend  && rating.recommend.length">
									<span class="icon-thumb_up"></span>
									<span class="item" v-for="item in rating.recommend">{{item}}</span>
								</div>
								<div class="time">
									{{rating.rateTime | formatDate}}
								</div>
							</div>
						</li>
					</ul>
				</div>
			</div>
	</div>
</template>

<script type="text/ecmascript-6">
import BSCroll from 'better-scroll';
import star from 'components/star/star';
import split from 'components/split/split';
import {formatDate} from 'common/js/date';
import ratingselect from 'components/ratingselect/ratingselect';
const ALL = 2;
const ERR_OK = 0;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				ratings: [],
				selectType: ALL,
				onlyContent: true
			};
		},
		created() {
			this.$http.get('./api/ratings').then((response) => {
				response = response.body;
				if (response.errno === ERR_OK) {
					this.ratings = response.data;
					this.$nextTick(() => {
						this.scroll = new BSCroll(this.$els.ratings, {
							click: true
						});
					});
				}
			});
		},
		methods: {
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
			star,
			ratingselect,
			split
		}
	};
</script>

<style lang="scss" rel="stylesheet/scss">
@import "../../common/sass/minxin";
	.ratings{
		position:absolute;
		top:4.606667rem;
		bottom:0;
		width:100%;
		left:0;
		overflow:hidden;
		.overview{
			display:flex;
			padding:0.48rem 0;
			.overview-left{

				flex:0 0 3.653333rem;
				width:3.653333rem;
				border-right:1px solid rgba(7,17,27,.1);
				text-align:center;
				padding:0.16rem 0;
				.score{
					line-height:0.746667rem;
					font-size:0.64rem;
					color:rgb(255,153,0);
					margin-bottom:0.16rem;
				}
				.title{
					line-height:0.32rem;
					font-size:0.32rem;
					color:rgb(7,17,27);
					margin-bottom:0.213333rem;
				}
				.rank{
					font-size:0.266667rem;
					line-height:0.266667rem;
					color:rgb(147,153,159);

				}
			}
			.overview-right{
				flex:1;
				padding: 0.16rem 0.64rem;
				.score-wrapper{
					margin-bottom:0.213333rem;
					font-size:0;
					.star{
						display:inline-block;
						margin:0 0.32rem;
						vertical-align:top;
					}
					.score{
						display:inline-block;
						vertical-align:top;
						font-size:0.32rem;
						color:rgb(255,153,0);
					}
				}
				.title{
						display:inline-block;
						line-height:0.48rem;
						vertical-align:top;
						font-size:0.32rem;
						color:rgb(7,17,27);
				}
				.delivery{
						margin: 0 0.32rem;
						font-size:0.32rem;
						color:rgb(147,153,159);
				}
			}
		}
		.rating-wrapper{
			padding:0 0.48rem;
			.rating-item{
				display:flex;
				padding: 0.48rem 0;
				@include border-1px-tb(bottom,rgba(7,17,27,.1));
				.avatar{
					flex:0 0 0.746667rem;
					width:0.746667rem;
					margin-right:0.32rem;
					img{
						border-radius:50%;
						width:0.746667rem;
						height:0.746667rem;
					}
				}
				.content{
					position:relative;
					flex:1;
					.name{
						margin-bottom:0.106667rem;
						line-height:0.32rem;
						font-size:0.266667rem;
						color:rgb(7,17,27);
					}
					.star-wrapper{
						margin-bottom:0.16rem;
						font-size:0;
						.star{
							display:inline-block;
							vertical-align:top;
							margin-right:0.16rem;
						}
						.delivery{
							display:inline-block;
							vertical-align:top;
							line-height:0.32rem;
							font-size:0.266667rem;
							color:rgb(147,153,159);
						}
					}
					.text{
						margin-bottom:0.213333rem;
						line-height:0.48rem;
						color:rgb(7,17,27);
						font-size:0.32rem;
					}
					.recommend{
						line-height:0.426667rem;
						font-size:0;
						.icon-thumb_up, .item{
							display:inline-block;
							margin: 0 0.213333rem 0.106667rem 0;
							font-size:0.24rem;
						}
						.icon-thumb_up{
							color:rgb(0,160,220);
						}
						.item{
							padding:0 0.16rem;
							border:1px solid rgba(7,17,27,.1);
							border-radius:0.026667rem;
							color:rgb(147,153,159);
							background:#fff;
						}
					}
					.time{
						position:absolute;
						top:0;
						right:0;
						line-height:0.32rem;
					}
				}
			}
		}
	}
</style>