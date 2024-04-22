<script setup lang="ts">
const props = defineProps<{
	tile?: number | string;
}>();

const isEmptyTile = computed<boolean>(() => props.tile == 0);

const getTileValue = computed(() => (isEmptyTile.value ? "" : props.tile));
</script>

<template>
	<div
		class="tile w-full h-28 flex items-center justify-center bg-green-500 rounded-md border border-green-700 transform transition ease-out duration-200 hover:scale-105"
		:class="{ empty: isEmptyTile }"
	>
		{{ getTileValue }}
	</div>
</template>

<style>
.empty {
	@apply bg-transparent border border-gray-500 pointer-events-none;
}
</style>
