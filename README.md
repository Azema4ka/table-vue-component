<img src="https://camo.githubusercontent.com/728ce9f78c3139e76fa69925ad7cc502e32795d2/68747470733a2f2f7675656a732e6f72672f696d616765732f6c6f676f2e706e67" width="100px" alt="vue-logo"/></img>
Table Vue Component
---
Description
---
>Table Vue Component is a component with which you can quickly build reactive tables.
Installation
---
**First step**
>**npm**
npm install tableVueComponent

>**yarn**
yarn add tableVueComponent
**Second step**
>**Global registration**
Vue.use(VueTableDynamic)

>**Local registration**
```js
<script>
	import TableVueComponent from 'tableVueComponent';
	export default: {
		components: {
			TableVueComponent
		}
	}
</script>
```

Props
---
|Prop|Type|Default|Description|
|:--:|:--:|:-----:|:---------:|
|columns|Array</br>```[{name: String, key: String}]```|[ ]|An array where each object is considered a column.</br>Object Values - `name: column name`, `key: key to the column data value`.|
|data|Array|[ ]|An array with the data you want to show in the table.
|removalFunction|Function|undefined|The function by which the deletion of selected data will be executed.|
|typeDataDelete|String</br>`updateData`</br>or</br>`deleteData`|updateData|Used in `removalFunction(typeDataDelete)` as the only parameter.</br>`updateData` - returns an updated data array with already deleted data.</br>`deleteData` - returns an array of data to be deleted.|
|abilitySelect|Boolean|true|`true` - allows you to choose which columns will be displayed.</br>`false` - disables this feature.|
|abilitySort|Boolean|true|`true` - makes it possible to sort data in a table.</br>`false` - disables this feature.|
