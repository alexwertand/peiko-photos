<template>
	<div class="container">
		<div class="table-box">
			<button
				type="button"
				class="get-data-btn"
				@click="getData">
				get data
			</button>

			<div v-if="isDataLoadedError">no data</div>

			<div v-else class="table">
				<div class="table-row table-head">
					<div class="table-cell">Stock</div>

					<div class="table-cell">Current</div>

					<div class="table-cell">Change</div>
				</div>

				<div
					v-for="(stock, index) in stocks"
					:key="index"
					class="table-row">

					<div class="table-cell">{{ stock.name }}</div>

					<div class="table-cell">{{ stock.current }}</div>

					<div class="table-cell" :class="[stock.change >=0 ? 'positive' : 'negative']">{{ stock.change }}</div>
				</div>
			</div>

			<div
				class="preloader"
				:class="{'is-hidden': !isDataLoading}">loading...</div>
		</div>
	</div>
</template>

<script>
	import { payload } from '../js/data';
	import getDataFunc from '../js/getDataFunc';

	export default {
		data() {
			return {
				isDataLoading: false,
				isDataLoadedError: false,
				stocks: []
			}
		},
		created() {
			this.getData();
		}, 
		methods: {
			getData() {
				this.isDataLoading = true;

				this.stocks.splice(0);

				getDataFunc(payload)
				.then(response => {
					if (this.isDataLoadedError) this.isDataLoadedError = false;

					this.handleResponse(response);
				})
				.catch(() => {
					this.isDataLoadedError = true;
				}).finally(() => {
					this.isDataLoading = false;
				}); 
			},
			handleResponse(response) {
				response.stocks.forEach((stock, index) => {
					let current = response.current[index],
						start = response.start[index],
						change = (current - start).toFixed(2);
						
					this.$set(this.stocks, index, {
						name: stock,
						change: change >=0 ? `+${change}`: `${change}`,
						current: current.toFixed(2),
					});
				});

				this.getOrderedStocksData();
			},
			getOrderedStocksData() {
				return this.stocks.sort((a, b) => a.name.localeCompare(b.name, 'en'));
			}
		},
	}
</script>

<style lang="scss">
	@mixin d-flex {
		display: flex;
	}
 
	@mixin flex-column {
		flex-direction: column;
	}

	@mixin flex-centering {
		justify-content: center;
		align-items: center;
		align-content: center;
	}

	@mixin margin-horizontal-centering {
		margin: 0 auto;
	}

	@mixin text-uppercase {
		text-transform: uppercase;
	}

	@mixin text-center {
		text-align: center;
	}

	@mixin text-right {
		text-align: right;
	}

	@mixin success {
		color: green;
	}

	@mixin error {
		color: red;
	}

	@mixin background-color-primary {
		background-color: rgb(255, 255, 255);
	}

	@mixin background-color-secondary {
		background-color: rgb(230, 230, 230);
	}

	@mixin outline-none {
		outline: none;
	}

	@mixin border-none {
		border: none;
	}

	@mixin border-simple-all {
		border: 1px solid rgb(199, 199, 199);
	}

	@mixin border-simple-bottom {
		border-bottom: 1px solid rgb(199, 199, 199);
	}

	@mixin f-bold {
		font-weight: bold;
	}

	@mixin relative {
		position: relative;
	}

	@mixin absolute {
		position: absolute;
	}

	@mixin is-hidden {
		display: none!important;
	}

	.is-hidden {
		@include is-hidden;
	}

	button {
		@include background-color-primary;
		@include outline-none;
		@include border-none;
		padding: 15px;
	}

	.container {
		@include d-flex;
		@include flex-centering;
		@include margin-horizontal-centering;
		width: 1280px;
	}

	.preloader {
		@include d-flex;
		@include flex-centering;
		@include absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(255, 255, 255, 0.75);
		z-index: 1;
	}

	.get-data-btn {
		@include background-color-secondary;
		@include text-uppercase;
		@include border-simple-all;
		margin-bottom: 25px;
		cursor: pointer;
	}

	.table-box {
		@include d-flex;
		@include flex-column;
		@include flex-centering;
		@include relative();
		width: 500px;
		margin: 100px auto;
		padding: 50px;
		background-color: #fff;
		border: 2px solid grey;
	}

	.table {
		@include border-simple-all;
		width: 80%;
	}

	.table-row {
		@include d-flex;
	}

	.table-cell {
		@include border-simple-bottom;
		width: 33.33333%;
		padding: 10px 15px;
	}

	.table-row:last-child {
		.table-cell {
			@include border-none;
		}
	}

	.table-head {
		.table-cell {
			@include f-bold;
			@include background-color-secondary;
		}
	}

	.table-cell {
		&:nth-child(2) {
			@include text-center;
		}

		&:nth-child(3) {
			@include text-right;
		}
	}

	.positive {
		@include success;
	}

	.negative {
		@include error;
	}
</style>
