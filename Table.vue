<template>
	<div class="table-wrapper">
		<div class="header">
			<div class="dropMenu-wrapper buttonSortColumns" v-if="abilitySort">
				<button 
					class="dropMenu_buttonDrop"
					:class="{dropMenu_buttonDropActive: buttonSortColumnsActive}" 
					@click="buttonSortColumnsActive = !buttonSortColumnsActive"
				>
					Sorting by {{sortActive == null ? 'none' : sortActive}}
					<svg width="28" height="28" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<path fill-rule="evenodd" clip-rule="evenodd" d="M10.9375 8L14.9375 12L10.9375 16L10 15.0625L13.0625 12L10 8.9375L10.9375 8Z" fill="#333333"/>
					</svg>
				</button>
				<div class="dropMenu" v-show="buttonSortColumnsActive">
					<button 
						class="dropMenu_buttonSelection"
						v-for="(column, index) in columns"
						:key="index"
						@click="sortActive == column.key ? false : sortActive = column.key"
					>
						{{column.name}}
					</button>
				</div>
			</div>
			<button 
				class="delete" 
				:disabled="deletItems.length < 1" 
				@click="deleteData"
			>
				Delete {{deletItems.length}}
			</button>
			<div class="dropMenu-wrapper buttonShowColumns" v-if="abilitySelect">
				<button 
					class="dropMenu_buttonDrop"
					:class="{dropMenu_buttonDropActive: buttonShowColumnsActive}" 
					@click="buttonShowColumnsActive = !buttonShowColumnsActive"
				>
					{{this.initShowColumns.length}} columns selected
					<svg width="28" height="28" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<path fill-rule="evenodd" clip-rule="evenodd" d="M10.9375 8L14.9375 12L10.9375 16L10 15.0625L13.0625 12L10 8.9375L10.9375 8Z" fill="#333333"/>
					</svg>
				</button>
				<div class="dropMenu" v-show="buttonShowColumnsActive">
					<div 
						class="dropMenu_boxSelected" 
						v-for="(column, index) in columns"
						:key="index"
					>
						<div class="checkbox-wrapper">
							<input type="checkbox" :id="index" :value="column" v-model="initShowColumns">
							<label :for="index"></label>
						</div>
						<p class="descript">{{column.name}}</p>
					</div>
				</div>
			</div>
		</div>
		<div class="body">
			<div class="columnUnique" v-show="initShowColumns.length > 0">
				<div class="title">
					<div class="checkbox-wrapper">
						<input type="checkbox" id="allDelete" @change="allCheckBoxs" v-model="allCheckBoxsActive">
						<label for="allDelete"></label>
					</div>
				</div>
				<div class="info">
					<div 
						class="boxUnique" 
						v-for="(item, index) in data"
						:key="index"
					>
						<div class="checkbox-wrapper">
							<input type="checkbox" :id="item.name" :value="item" v-model="deletItems">
							<label :for="item.name"></label>
						</div>
					</div>
				</div>
			</div>
			<div 
				class="column"
				v-for="(column, index) in columns"
				:key="index"
				v-show="getStatusShowColumn(column)"
			>
				<div class="title">
					{{column.name}}
				</div>
				<div class="info">
					<div 
						class="box"
						v-for="(item, index) in data"
						:key="index"
					>
						{{item[column.key]}}
					</div>
				</div>
			</div>
		</div>
		<div class="warning" v-if="data.length == 0">Data is empty!</div>
	</div>
</template>

<script>
export default {
	props: {
		columns: {
			type: Array,
			default() {
				return [];
			},
			validator(value) {
				for(let i = 0; i < value.length; i++) {
					const keys = Object.keys(value[i]);
					let objectName = false, objectKey = false;
					for(let j = 0; j < keys.length; j++) {
						if(keys[j] == 'name') objectName = true;
						else if(keys[j] == 'key') objectKey = true;
					}
					if(!objectName || !objectKey) throw new Error('Prop Columns does not have name or key properties!');
				}
				return value;
			}
		},
		data: {
			type: Array,
			default() {
				return [];
			}
		},
		removalFunction: Function,
		typeDataDelete: {
			type: String,
			default: 'updateData',
			validator(value) {
				if(value != 'updateData' && value != 'deleteData') throw new Error('Prop typeDataDelete has an unknown value!')
				else return value;
			}
		},
		abilitySelect: {
			type: Boolean,
			default: true
		},
		abilitySort: {
			type: Boolean,
			default: true
		}
	},
	data() {
		return {
			buttonSortColumnsActive: false,
			buttonShowColumnsActive: false,
			allCheckBoxsActive: false,
			initShowColumns: [...this.columns],
			deletItems: [],
			sortActive: null
		}
	},
	methods: {
		getStatusShowColumn(column) {
			for(let i = 0; i < this.initShowColumns.length; i++) {
				if(this.initShowColumns[i].name == column.name) return true
			}
		},
		deleteData() {
			if(this.deletItems.length > 0) {
				if(this.typeDataDelete == 'updateData') {
					let newData = [...this.data];
					for(let i = 0; i < this.deletItems.length; i++) {
						for(let j = 0; j < newData.length; j++) {
							if(this.deletItems[i] == newData[j]) {
								newData.splice(j, 1);
								break;
							}
						}
					}
					this.allCheckBoxsActive = false;
					this.deletItems.length = 0;
					this.removalFunction(newData);
				}
				else if(this.typeDataDelete == 'deleteData') this.removalFunction(this.deletItems);
				else throw new Error('Prop typeDataDelete has an unknown value!');
			}
		},
		allCheckBoxs() {
			if(!this.allCheckBoxsActive) this.deletItems.length = 0;
			else this.deletItems = [...this.data]
		},
		sort(a, b) {
			if(a[this.sortActive] < b[this.sortActive]) return -1
			else if(a[this.sortActive] > b[this.sortActive]) return 1
			else return 0;
		}
	},
	watch: {
		sortActive(val) {
			this.data.sort(this.sort);
		}
	}
}
</script>

<style lang="scss" scoped>
* {
	margin: 0;
	padding: 0;
}

button,
input {
	outline: none;
	border: none;
}

.table-wrapper {
	.checkbox-wrapper {
		input {display: none;}

		input:checked + label {
			background: #00A11E;
			border: 1px solid #00A11E;
			
			&::after {
				display: flex;
				align-items: center;
				justify-content: center;
				position: absolute;
				top: 0;
				left: 0;
				content: '✔';
				font-size: 12px;
				color: white;
				width: 100%;
				height: 100%;
			}
		}

		label {
			box-sizing: border-box;
			cursor: pointer;
			position: relative;
			display: block;
			width: 15px;
			height: 15px;
			background: white;
			border: 1px solid #C6CBD4;
			border-radius: 2px;
			transition: 0.3s background ease, 0.3s border ease;
		}
	}

	.header {
		display: flex;
		align-items: center;
		justify-content: space-evenly;
		flex-wrap: wrap;

		.dropMenu-wrapper {
			position: relative;

			.dropMenu_buttonDrop {
				display: flex;
				align-items: center;
				box-sizing: border-box;
				padding: 8px 12px;
				color: black; 
				font-size: 14px;
				background: none;
				border: 1px solid #C6CBD4;
				border-radius: 5px;

				svg {
					transition: 0.3s transform ease;
					transform: rotate(90deg);
				}
			}

			.dropMenu_buttonDropActive {
				svg {
					transform: rotate(-90deg);
				}
			}

			.dropMenu {
				z-index: 1;
				position: absolute;
				width: 100%;
				box-sizing: border-box;
				padding: 6px 0px;
				display: flex;
				flex-direction: column;
				align-items: flex-start;
				background: white;
				box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.2);
				border-radius: 5px;
				
				.dropMenu_buttonSelection {
					width: 100%;
					background: transparent;
					color: black;
					font-size: 14px;
					padding: 8px 0px;
					&:hover {background: rgb(240, 240, 240);}
				}

				.dropMenu_boxSelected {
					box-sizing: border-box;
					display: flex;
					align-items: center;
					padding: 8px 0px 8px 17px;

					.descript {
						cursor: default;
						margin-left: 13px;
						color: black;
						font-size: 14px;
					}
				}
			}
		}

		.delete {
			box-sizing: border-box; 
			padding: 13px 12px;
			color: white;
			background: rgb(0,161,36);
			border: 1px solid rgb(0,161,36);
			font-size: 14px;
			border-radius: 5px;

			&:disabled {
				color: #C6CBD4;
				background: white;
				border: 1px solid #C6CBD4;
			}
		}
	}

	.body {
		display: flex;
		align-items: flex-start;
		justify-content: space-around;

		.columnUnique {
			width: 100%;

			.title {
				display: flex;
				align-items: center;
				justify-content: center;
				height: 50px;
				font-weight: 500;
				padding: 0px 20px;
				border-bottom: 2px solid rgb(237,237,237);
			}

			.info {
				.boxUnique {
					display: flex;
					align-items: center;
					justify-content: center;
					height: 40px;
					padding: 0px 20px;
				}
			}
		}

		.column {
			width: 100%;

			.title {
				display: flex;
				align-items: center;
				justify-content: center;
				height: 50px;
				font-weight: 500;
				border-bottom: 2px solid rgb(237,237,237);
			}

			.info {
				.box {
					display: flex;
					align-items: center;
					justify-content: center;
					height: 40px;
				}
			}
		}
	}

	.warning {
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 20px;
		font-weight: 500;
		padding: 20px 0px;
	}
}
</style>