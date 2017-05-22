<template>
	<div class="ratingselect">
		<div class="rating-type">
			<span @click="select(2,$event)" class="block positive" :class="{'active':selectType === 2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
			<span  @click="select(0,$event)" class="block positive" :class="{'active':selectType === 0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
			<span  @click="select(1,$event)" class="block negative" :class="{'active':selectType === 1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
		</div>
		<div class="switch" :class="{'on':onlyContent}" @click="toggleContent($event)">
			<span>
			<svg class="icon icon-check_circle" :class="{'highlight':totalCount > 0}" aria-hidden="true">
		  			<use xlink:href="#icon-check_circle"></use>
				</svg>
			</span>
			<span class="text">只看有内容的评价</span>
		</div>
	</div>
</template>
<script type="text/esmascript-6">
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		props: {
			ratings: {
				type: Array,
				default() {
					return [];
				}
			},
			selectType: {
				type: Number,
				default: ALL
			},
			onlyContent: {
				type: Boolean,
				default: false
			},
			desc: {
				type: Object,
				default() {
					return {
						all: '全部',
						negative: '不满意',
						positive: '满意'
					}
				}
			}
		},
		computed: {
			positives() {
				return this.ratings.filter((rating) => {
					return rating.rateType === POSITIVE;
				});
			},
			negatives() {
				return this.ratings.filter((rating) => {
					return rating.rateType === NEGATIVE;
				});
			}
		},
		methods: {
			select(type,event){
				if(!event._constructed){
					return;
				}
				this.selectType = type;
				this.$dispatch('ratingtype.select', type);
			},
			toggleContent(event){
				if(!event._constructed){
					return;
				}
				this.onlyContent = !this.onlyContent;
				this.$dispatch('content.toggle',this.onlyContent);
			}
		}
	};
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../common/sass/minxin.scss";
	.ratingselect{
		.rating-type{
			padding:0.48rem 0;
			margin:0 0.48rem;
			@include border-1px-tb(bottom,rgba(7,17,27,.2));
			font-size:0;
			.block{
				display:inline-block;
				padding:0.213333rem 0.32rem;
				margin-right:0.213333rem;
				border-radius:0.026667rem;
				color:rgb(77,85,93);
				font-size:0.32rem;
				.count{
					font-size:0.213333rem;
					margin-left:0.053333rem;
				}
				&.positive{
					background:rgba(0,160,220,.2);
					&.active{
						background:rgb(0,160,220);
					}
				}
				&.negative{
					background:rgba(77,85,93,.2);
					&.active{
						background:rgb(77,85,93);
						color:#fff;
					}
				}
			}
		}
		.switch{
			padding:0.32rem 0.48rem;
			line-height:0.64rem;
			border-bottom:1px solid rgba(7,17,27,.2);
			color:rgb(147,153,159);
			font-size:0;
			&.on{
				.icon{
					color:#00c850;
				}
			}
			.icon{
				display:inline-block;
				margin-left:0.106667rem;
				font-size:0.64rem;
			}
			.text{
				font-size:0.32rem;
				vertical-align:top;
			}
		}
	}
</style>