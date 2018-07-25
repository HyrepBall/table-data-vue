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
			<div class="double-table-container" @scroll.passive="scrollHanle">
				<div class="order-table">
					<div class="order-table__header" :style="{width: `${returnHeaderWidth()}px`}">
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
						<div class="order-table__row" v-for="(item, i) in tableLeftList">
							<div class="order-table__chart-line left-chart"
							:style="{width: `${calcChartWidth(item)}px`}"></div>
							<div class="order-table__count">
								{{i}}
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
					<div class="order-table__header" :style="{width: `${returnHeaderWidth()}px`}">
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
						<div class="order-table__row" v-for="(item, i) in tableRightList">
							<div
								class="order-table__chart-line right-chart" 
								:style="{width: `${calcChartWidth(item)}px`}"></div>
							<div class="order-table__count">
								{{i}}
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
		</div>
	</div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

import JSONdata from './utils/1.json';

import './styles/style.scss'

export default {
	name: "app",
	data() {
		return {
			JSONdata,
			importedDataArray: [],
			readyToRenderArray: [],
			tableLeftList: [],
			tableRightList: [],
			settings: {
        maxScrollbarLength: 60
      }
		}
	},
	created() {
		this.importedDataArray = JSONdata.asks.slice(0,1000)
	},
	beforeMount() {
		this.filterToRender()
	},
	methods: {
		filterToRender() {
			let amount = []
			let total = []

			const filteredToRender = this.importedDataArray.reduce((prevItem, currentItem, i, arr) => {

				if (i === 0) {
					total = currentItem.quantity
				} else {
					total = total + currentItem.quantity
				}

				amount.push({
					count: 1,
					total: total.toFixed(6),
					amount: currentItem.quantity.toFixed(6),
					price: currentItem.price
				})
				return amount
			}, []);

			this.readyToRenderArray = filteredToRender
			this.tableLeftList = this.readyToRenderArray.slice(0, 20)
			this.tableRightList = this.readyToRenderArray.slice(this.tableLeftList.length +1, this.tableLeftList.length +21)
		},
		renderTables(quantity) {
			this.tableLeftList = this.readyToRenderArray.slice(0, quantity)
			this.tableRightList = this.readyToRenderArray.slice(quantity +1, quantity + quantity +1)
		},
		calcChartWidth(item) {
			// item.total === 10000 === 100% === 500 px
			let chartWidthValue = Math.round(item.total) / 500 * 100
			return chartWidthValue.toFixed(1)
		},
		scrollHanle(evt) {
      let tableScrollPosition = document.querySelector('.double-table-container').scrollTop
      let tableContentHeight = document.querySelector('.double-table-container').scrollHeight
      let tableHeight = document.querySelector('.double-table-container').clientHeight

      if (tableScrollPosition === (tableContentHeight - tableHeight)) {
      	// подгрузить 5 объектов
      	this.renderTables(this.tableLeftList.length + 5)
      	
      	console.log('Должны подгрузится данные т.к. state массива, из которого рендерится - обновился')
      }
    },
    returnHeaderWidth() {
    	return document.querySelector('.order-table').clientWidth
    }
	},
	components: {
		HelloWorld,
	},
};
</script>

<style>

</style>
