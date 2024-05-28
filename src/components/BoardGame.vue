<template>
  <div>
    <p>TIME: {{formattedTime}}</p>
  </div>
  <div class="sudoku-board">
    <table>
      <tbody>
        <tr v-for="(row, rowIndex) in board" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex" :class="getClass(rowIndex, colIndex)">
            <input 
              type="text" 
              maxlength="1"
              v-model="board[rowIndex][colIndex].value" 
              :class="board[rowIndex][colIndex].class"
              :disabled = "board[rowIndex][colIndex].class == 'default' ? true : false "
              @click="startTime"
            />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <button>Check Sudoku</button>
  <button @click="startTime">Start</button>
  <button @click="stopTimer">Stop</button>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const createBoard = () => {
  return Array.from({ length: 9 }, () => Array.from({ length: 9 }, () => ({ value: '', class: '' })))
}

const board = ref(createBoard())
let time = ref(null)
let timerInterval = null

onMounted(() => {
  fillBoard()
})
const formattedTime = computed(() => {
  const minutes = Math.floor(time.value / 60)
  const seconds = time.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

const startTime = () => {
  if (timerInterval) {
    clearInterval(timerInterval)
  }
  time.value = time.value !== 0 ? time.value : 0
  timerInterval = setInterval(() => {
    time.value++
  }, 1000)
}
const stopTimer = () => {
  clearInterval(timerInterval)
}

const fillBoard = () => {
  for (let i = 0; i < 20; i++) {
    const randomCol = Math.floor(Math.random() * 9)
    const randomRow = Math.floor(Math.random() * 9)
    const randomNumber = Math.floor(Math.random() * 9) + 1 // Números de 1 a 9
    if (!duplicateRowCol(randomRow, randomCol, randomNumber)) {
      board.value[randomRow][randomCol] = { value: randomNumber, class: 'default' }
    }
  }
}

const getClass = (rowIndex, colIndex) => {
  let classes = [board.value[rowIndex][colIndex].class]
  if ((colIndex + 1) % 3 === 0 && colIndex !== 8) classes.push('right-border')
  if ((rowIndex + 1) % 3 === 0 && rowIndex !== 8) classes.push('bottom-border')
  return classes.join(' ')
}

const duplicateRowCol = (rowIndex, colIndex, value) => {
  // Verificar duplicados en la fila
  for (let i = 0; i < 9; i++) {
    if (i !== colIndex && board.value[rowIndex][i].value === value) {
      return true
    }
  }
  
  // Verificar duplicados en la columna
  for (let i = 0; i < 9; i++) {
    if (i !== rowIndex && board.value[i][colIndex].value === value) {
      return true
    }
  }
  return false
}
// const duplicateSquare = (rowIndex, colIndex, value)=> {
//   for (let i = 0; i < 9; i++) {
    
    
//   }
// }
</script>

<style scoped>
.sudoku-board {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

table {
  border-collapse: collapse;
}

td {
  width: 40px;
  height: 40px;
  text-align: center;
  border: 1px solid black;
}

input {
  width: 90%;
  height: 100%;
  text-align: center;
  font-size: 20px;
  border: none;
}

.bottom-border {
  border-bottom: 2px solid black;
}

.right-border {
  border-right: 2px solid black;
}
.default{
  font-weight: 700;
  color: black;
}
</style>
