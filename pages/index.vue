<script setup lang="ts">
const tiles = ref<number[]>([1, 2, 0, 4, 5, 6, 7, 8, 9, 3]);
</script>

<template>
	<section class="w-full mt-28 flex justify-center">
		<TheBoard :tiles="tiles" />
	</section>
</template>
