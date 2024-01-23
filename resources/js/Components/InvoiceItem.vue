<script setup>
import RoundAddButton from '@/Components/RoundAddButton.vue';
import RoundDeleteButton from '@/Components/RoundDeleteButton.vue';

defineProps({
	title: String,
	description: String,
	quantity: Number,
	price: Number,
	discount: Number,
	vat: Number,
	subtotal: Number,
	index: Number,
});

const vatValues = [21, 10, 4];

</script>

<script>
export default {
	methods: {
		newItem() {
			this.$emit('item-added');
		},
		removeItem(index) {
			this.$emit('item-removed', index);
		},
		valueChanged(index, field, value) {
			if(field != 'title' && field != 'description') value = parseFloat(value);

			const newVal = {
				index: index,
				field: field,
				value: value,
			};

			this.$emit('value-changed', newVal);
			
		},
		formatValue(value) {
            return new Intl.NumberFormat('de-DE', { style: 'currency', currency: 'EUR' }).format(
                value
            );
        }
	},
	emits: [
		'item-added',
		'item-removed',
		'value-changed',
	],
}
</script>

<template>
	<div class=" space-y-4">
		<div class="grid grid-cols-10 gap-2 py-2">
			<div class="col-span-4 max-sm:col-span-10">
				<input placeholder="Sample Item " class="inp" :value="title" @change="valueChanged(index, 'title', $event.target.value)">
				<textarea class="inp" @change="valueChanged(index, 'description', $event.target.value)">{{ description }}</textarea>
			</div>
			<div class="col-span-1 max-sm:col-span-10">
				<input type="number" class="inp text-right" :value="quantity" @change="valueChanged(index, 'quantity', $event.target.value)">
			</div>
			<div class="col-span-1 max-sm:col-span-10">
				<input type="number" step='0.01' class="inp text-right" :value="price" @change="valueChanged(index, 'price', $event.target.value)">
			</div>
			<div class="col-span-1 max-sm:col-span-10">
				<input type="number" step='0.01' class="inp text-right" :value="discount" @input="valueChanged(index, 'discount', $event.target.value)">
			</div>
			<div class="col-span-1 max-sm:col-span-10">
				<select class="inp text-center" @change="valueChanged(index, 'vat', $event.target.value)">
					<option :value="vatValues[0]" :selected="vatValues[0] == vat">{{ vatValues[0] }} </option>
					<option :value="vatValues[1]" :selected="vatValues[1] == vat">{{ vatValues[1] }} </option>
					<option :value="vatValues[2]" :selected="vatValues[20] == vat">{{ vatValues[2] }} </option>
				</select>
			</div>
			<div class="col-span-1 max-sm:col-span-10">
				<input type="text" class="inp text-right" :value="formatValue(subtotal)" @input="valueChanged(index, 'subtotal', $event.target.value)">
				
			</div>
			<div class="col-span-1 max-sm:col-span-10 flex space-around">
				<RoundAddButton @click="newItem()" />
				<span v-if="index > 0"><RoundDeleteButton @click="removeItem(index)" /></span>

				
			</div>
			
		</div>


	</div>

	<hr>
</template>