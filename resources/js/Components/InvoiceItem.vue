<script setup>
import RoundAddButton from '@/Components/RoundAddButton.vue';
import RoundDeleteButton from '@/Components/RoundDeleteButton.vue';

defineProps({
	title: String,
	description: String,
	quantity: Number,
	price: Number,
	subtotal: Number,
	index: Number,
});

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
			if(field != 'title' && field != 'description') value = parseInt(value);

			const newVal = {
				index: index,
				field: field,
				value: value,
			};

			this.$emit('value-changed', newVal);
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
			<div class="col-span-4">
				<input placeholder="Sample Item " class="inp" :value="title" @change="valueChanged(index, 'title', $event.target.value)">
				<textarea class="inp" @change="valueChanged(index, 'description', $event.target.value)">{{ description }}</textarea>
			</div>
			<div class="col-span-1">
				<input class="inp" :value="quantity" @change="valueChanged(index, 'quantity', $event.target.value)">
			</div>
			<div class="col-span-2">
				<input class="inp" :value="price" @change="valueChanged(index, 'price', $event.target.value)">
			</div>
			<div class="col-span-2">
				<input class="inp" :value="subtotal" @change="valueChanged(index, 'subtotal', $event.target.value)">
			</div>
			<div class="col-span-1">
				<RoundAddButton @click="newItem()" />
				<span v-if="index > 0"><RoundDeleteButton @click="removeItem(index)" /></span>

				
			</div>
			
		</div>


	</div>

	<hr>
</template>