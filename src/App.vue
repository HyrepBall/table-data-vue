<template>
	<div id="app">
		<div class="widget-card">
			<div class="widget-header">
				<div class="widget-title-container">
					<span class="widget-title__dd button"><img src="./assets/dd-arr.png" alt=""></span>
					<h2 class="widget-title">Order Book</h2>
				</div>
				<div class="widget-header-actions">
					<span class="button"><img src="./assets/info.png" alt=""></span>
					<span class="button"><img src="./assets/settings.png" alt=""></span>
				</div>
			</div>
			<VuePerfectScrollbar class="scroll-area" ref="scrollArea" v-once :settings="settings" @ps-scroll-y="scrollHanle">
			<div class="double-table-container">
				<div class="order-table">
					<div class="order-table__header">
						<div class="order-table__count">
							Count
						</div>
						<div class="order-table__total">
							Total
						</div>
						<div class="order-table__amount">
							Amount
						</div>
						<div class="order-table__price">
							Price
						</div>
					</div>
					<div class="order-table__body">
						<div class="order-table__row" v-for="item in tableLeftList">
							<div class="order-table__chart-line left-chart"
							:style="{width: `${calcChartWidth(item)}px`}"></div>
							<div class="order-table__count">
								1
							</div>
							<div class="order-table__total">
								{{item.total}}
							</div>
							<div class="order-table__amount">
								{{item.amount}}
							</div>
							<div class="order-table__price">
								{{item.price}}
							</div>
						</div>
					</div>
				</div>
				<div class="order-table revert">
					<div class="order-table__header">
						<div class="order-table__count">
							Count
						</div>
						<div class="order-table__total">
							Total
						</div>
						<div class="order-table__amount">
							Amount
						</div>
						<div class="order-table__price">
							Price
						</div>
					</div>
					<div class="order-table__body">
						<div class="order-table__row" v-for="item in tableRightList">
							<div
								class="order-table__chart-line right-chart" 
								:style="{width: `${calcChartWidth(item)}px`}"></div>
							<div class="order-table__count">
								1
							</div>
							<div class="order-table__total">
								{{item.total}}
							</div>
							<div class="order-table__amount">
								{{item.amount}}
							</div>
							<div class="order-table__price">
								{{item.price}}
							</div>
						</div>
					</div>
				</div>
			</div>
			</VuePerfectScrollbar>
		</div>
	</div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import VuePerfectScrollbar from 'vue-perfect-scrollbar'

import JSONdata from './utils/1.json';

import './styles/style.scss'

export default {
	name: "app",
	data() {
		return {
			JSONdata,
			dataTableLeft: [],
			tableLeftList: [],
			dataTableRight: [],
			tableRightList: [],
			settings: {
        maxScrollbarLength: 60
      }
		}
	},
	created() {
		this.dataTableLeft = JSONdata.asks.slice(0,500)
		this.dataTableRight = JSONdata.asks.slice(501,1000)

	},
	beforeMount() {
		this.getLeftTableData()
		this.getRightTableData()
	},
	methods: {
		getLeftTableData() {
			let cutArr = this.dataTableLeft.slice(0, 50)
			let amount = []
			let total = []

			this.tableLeftList = cutArr.reduce((prevItem, currentItem, i, arr) => {

				if (i === 0) {
					total = currentItem.quantity
				} else
				if (i > 0) {
					total = total + currentItem.quantity
				}
				amount.push({
					count: 1,
					total: total.toFixed(6),
					amount: currentItem.quantity.toFixed(6),
					price: currentItem.price
				})
				return amount
			}, [])
			return this.tableLeftList
		},
		getRightTableData() {

			let cutArr = this.dataTableRight.slice(0, 50)
			let amount = []
			let total = []

			this.tableRightList = cutArr.reduce((prevItem, currentItem, i, arr) => {

				if (i === 0) {
					total = currentItem.quantity
				} else
				if (i > 0) {
					total = total + currentItem.quantity
				}
				amount.push({
					count: 1,
					total: total.toFixed(6),
					amount: currentItem.quantity.toFixed(6),
					price: currentItem.price
				})
				return amount
			}, [])
			return this.tableRightList
		},
		calcChartWidth(item) {
			// item.total === 10000 === 100% === 500 px
			let chartWidthValue = Math.round(item.total) / 500 * 100
			return chartWidthValue.toFixed(1)
		},
		scrollHanle(evt) {
      let tableScrollPosition = document.querySelector('.scroll-area').scrollTop
      let tableContentHeight = document.querySelector('.scroll-area').scrollHeight
      let tableHeight = document.querySelector('.scroll-area').clientHeight
      const downloadPosition = 300

      console.log(tableScrollPosition === (tableContentHeight - tableHeight))
    },
	},
	components: {
		HelloWorld,
		VuePerfectScrollbar,
	},
};
</script>

<style>

</style>
