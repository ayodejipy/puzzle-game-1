<script setup lang="ts">
import { useStorage } from "@vueuse/core";

interface IStore {
	user: string;
	time: Date | null;
	isSolved: boolean;
}

const props = defineProps<{
	tiles: number[];
}>();

const BOARD_GRID = 4;
const tiles = ref<number[]>(props.tiles);
const isSolved = ref<boolean>(false);
const boardRef = ref<HTMLDivElement | null>(null);

const store = useStorage<IStore>("game-board", { user: "", time: null, isSolved: false });

// const tiles = computed<number[]>(() => props.tiles);

function getEmptySpaceIndex() {
	return tiles.value.findIndex((tile) => tile === 0);
}

function isMoveValid(index: number) {
	const emptyTileIndex = getEmptySpaceIndex();
	const rowDiff = Math.abs(
		Math.floor(index / BOARD_GRID) - Math.floor(emptyTileIndex / BOARD_GRID)
	);
	const colDiff = Math.abs((index % BOARD_GRID) - (emptyTileIndex % BOARD_GRID));

	return (rowDiff === 1 && colDiff === 0) || (colDiff === 1 && rowDiff === 0);
}

function swapTiles(indexToMove: number, emptyIndex: number) {
	const temp = tiles.value[indexToMove];
	tiles.value[indexToMove] = tiles.value[emptyIndex];
	tiles.value[emptyIndex] = temp;
}

function moveTile(index: number) {
	const emptyTileIndex = getEmptySpaceIndex();
	if (isMoveValid(index)) {
		swapTiles(index, emptyTileIndex);

		if (isGameSolved()) {
			store.value = { user: "sonny"	, time: new Date(), isSolved: true };
			isSolved.value = true;
		}
	}
}

function isGameSolved() {
	for (let i = 0; i < tiles.value.length - 1; i++) {
		if (tiles.value[i] !== i + 1) {
			return false;
		}
	}
	return true;
}

function restartPuzzle() {
	for (let i = tiles.value.length - 1; i > 0; i--) {
		const index2 = Math.floor(Math.random() * (i + 1));
		[tiles.value[i], tiles.value[index2]] = [tiles.value[index2], tiles.value[i]];
	}
}
</script>

<template>
	<section class="w-3/6 flex flex-col gap-6">
		<div v-if="isSolved" class="bg-green-300 text-gray-900 w-full py-5 flex justify-center">
			Congratulations, you have completed the puzzle.
		</div>

		<div class="w-full flex justify-between">
			<div class="font-semibold text-sm">
				<ClientOnly>
					<p class="">User: {{ store.user }}</p>
					<p>Fastest time solved</p>

					<template #fallback>
						<!-- this will be rendered on server side -->
						<p>Loading user info...</p>
					</template>
				</ClientOnly>
			</div>
			<button
				v-if="isSolved"
				type="button"
				class="bg-yellow-300 text-gray-700 px-4 py-2 rounded transition duration-200 hover:ring-2 hover:ring-offset-2 hover:ring-yellow-400"
				@click="restartPuzzle"
			>
				Restart
			</button>
		</div>

		<div ref="boardRef" class="w-full grid grid-cols-4 gap-2 p-1 border border-slate-200">
			<TheTile
				v-for="(tile, index) in tiles"
				:key="tile"
				:tile="tile"
				@click="moveTile(index)"
			/>
		</div>
	</section>
</template>
