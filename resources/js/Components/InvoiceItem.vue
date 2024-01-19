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
		}
	},
	emits: [
		'item-added',
		'item-removed'
	],
}
</script>

<template>
	<div class=" space-y-4">
		<div class="grid grid-cols-10 gap-2 py-2">
			<div class="col-span-4">
				<input placeholder="Sample Item " class="inp" :value="title">
				<textarea class="inp">{{ description }}</textarea>
			</div>
			<div class="col-span-1">
				<input class="inp" :value="quantity">
			</div>
			<div class="col-span-2">
				<input class="inp" :value="price" >
			</div>
			<div class="col-span-2">
				<input class="inp" :value="subtotal">
			</div>
			<div class="col-span-1">
				<RoundAddButton @click="newItem()" />
				<span v-if="index > 0"><RoundDeleteButton @click="removeItem(index)" /></span>

				
			</div>
			
		</div>


	</div>

	<hr>
</template>