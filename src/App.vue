<script setup>
import { ref, reactive, computed } from 'vue'

const playerTurn = ref('❌')
const winner = ref(null)
const table = ref([
    [null, null, null], 
    [null, null, null], 
    [null, null, null]
])
const scoreboard = reactive({
    '❌': 0,
    '⭕': 0

})

const draw = computed(() => !winner.value && !table.value.flat().some(e => e === null) )
const gameEnded = computed(() => draw.value || winner.value )
const statusMessage = computed(() => {
    if (winner.value) {
        return `Player ${winner.value} Won!`
    }
    if (draw.value) {
        return 'The game is drawn!'
    }
    return `Player's ${playerTurn.value} turn.`
})

const getCell = (index) => table.value[Math.floor(index / 3)][index % 3]
const setCell = (index, value) => table.value[Math.floor(index / 3)][index % 3] = value
const makeMove = (index) => {
    setCell(index, playerTurn.value)
    if (checkWin(playerTurn.value)) {
        winner.value = playerTurn.value
        scoreboard[playerTurn.value]++
    }
    toggleTurn()
}
const toggleTurn = () => { playerTurn.value == '❌' ? playerTurn.value = '⭕' : playerTurn.value = '❌' }

const checkWin = (playerTurn) => {
    if (table.value.some(row => row.every(col => col === playerTurn ))) return true
    for (let col = 0; col < 3; col++) {
        if(table.value[0][col] === playerTurn && table.value[1][col] === playerTurn && table.value[2][col] === playerTurn) {
            return true
        }
    }
    if (table.value[0][0] === playerTurn && table.value[1][1] === playerTurn && table.value[2][2] === playerTurn) return true
    if (table.value[0][2] === playerTurn && table.value[1][1] === playerTurn && table.value[2][0] === playerTurn) return true

    return false
}
const restartGame = () => {
    table.value = [
    [null, null, null], 
    [null, null, null], 
    [null, null, null]
    ]
    playerTurn.value = '❌'
    winner.value = null
}
</script>
<template>  
    <div class="flex flex-col justify-center items-center h-full text-white">
        <span class="font-bold text-7xl mb-8">Tic-Tac-Toe</span>
        <span class="m-4 text-3xl">{{ statusMessage }}</span>
        <div class="fixed left-4 flex flex-col justify-center w-[50%]">
            <span v-for="(score, player) in scoreboard" class="text-4xl p-2  mx-8">{{ player }} - {{ score }}</span>
        </div>
        <div class="grid grid-cols-3 gap-0  w-144">
            <div v-for="(_, index) in 9" class="h-48 w-48 border-2">
                <button 
                class="h-full w-full text-5xl" 
                :disabled="getCell(index) || gameEnded"
                @click="makeMove(index)"
                >
                {{ getCell(index) }}
                </button>
            </div>
        </div>
        <div>

        </div>
        <button 
            v-if="gameEnded"
            @click="restartGame"
            class="m-8 p-4 text-3xl bg-cyan-500 rounded-xl"
        >
            Play Again?
        </button>
    </div>
</template>
