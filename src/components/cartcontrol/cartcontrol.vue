<template>
	<div class="cartcontrol">
		<div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreasecart" transition="move">
				<svg class="inner icon icon-remove_circle_outline" :class="{'highlight':totalCount>0}" aria-hidden="true">
			  		<use xlink:href="#icon-remove_circle_outline"></use>
				</svg>
		</div>
		<div class="cart-count" v-show="food.count>0">{{food.count}}</div>
		<div class="cart-add" @click.stop.prevent="addCart">
			<svg class="icon icon-add_circle"  :class="{'highlight':totalCount>0}" aria-hidden="true">
			  <use xlink:href="#icon-add_circle"></use>
			</svg>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
import Vue from 'vue';
	export default {
		props: {
			food: {
				type: Object
			}
		},
		methods: {
			addCart(event) {
				if (!event._constructed) {
				   return;
				}
				if (!this.food.count) {
					Vue.set(this.food, 'count', 1);
				} else {
					this.food.count++;
				}
				this.$dispatch('cart.add', event.target);
			},
			decreasecart(event) {
				if (!event._constructed) {
				   return;
				}
				if (this.food.count > 0) {
					this.food.count--;
				}
			}
		}
	};
</script>
<style lang="scss" rel="stylesheet/scss">
	.cartcontrol{
		font-size:0;
		.cart-decrease {
			display:inline-block;
			transition: all 0.4s linear;
			&.move-transition{
				opacity: 1;
				transform: translate3d(0,0,0);
				.inner{
					display:inline-block;
					transition: all 0.4s linear;
					transform: rotate(0deg);
				}
			}
			&.move-enter,&.move-leave{
				opacity: 0;
				transform:translate3d(0.64rem, 0, 0);
				.inner{
					transform: rotate(180deg);
				}
			}
			.icon.icon-remove_circle_outline{
				width:0.64rem;
				height:0.64rem;
				color:rgb(0,160,220);
				margin-right:0;
			}
			
		}
		.cart-count{
			display:inline-block;
			width:0.42rem;
			padding-top: 0.16rem;
			text-align:center;
			vertical-align:top;
			font-size:0.266667rem;
			color:rgb(137,153,159);
		}
		.cart-add{
			display:inline-block;
			.icon.icon-add_circle{
				width:0.64rem;
				height:0.64rem;
				color:rgb(0,160,220);
				margin-right:0;
			}
		}
	}
</style>