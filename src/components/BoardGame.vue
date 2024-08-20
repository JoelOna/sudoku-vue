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
  <section class="buttons">
    <button @click="checkGame">Check Sudoku</button>
    <button @click="startTime">Start</button>
    <button @click="stopTimer">Stop</button>
    <button @click="restart">Restart</button>

  </section>
  <div>
    <p v-if="gameWin == 'win'" style="color: green;">Win</p>
    <p v-if="gameWin == 'lose'" style="color:red;">Lose</p>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const createBoard = () => {
  return Array.from({ length: 9 }, () => Array.from({ length: 9 }, () => ({ value: null, class: '' })))
}

const board = ref(createBoard())
let time = ref(null)
let timerInterval = null
const gameWin = ref()


onMounted(() => {
  fillBoard()
})
const restart = () => {
   board.value.forEach((row, rowIndex) => {
    row.forEach((cell, colIndex) => {
      board.value[rowIndex][colIndex] = { value: null, class: '' }
    })
  })
  // Rellenar nuevamente el tablero
  fillBoard()

  // Reiniciar el tiempo
  time.value = 0
  stopTimer()
}
// Formatear el tiempo
const formattedTime = computed(() => {
  const minutes = Math.floor(time.value / 60)
  const seconds = time.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

//Empezar el tiempo
const startTime = () => {
  if (timerInterval) {
    clearInterval(timerInterval)
  }
  time.value = time.value !== 0 ? time.value : 0
  timerInterval = setInterval(() => {
    time.value++
  }, 1000)
}
//Para el tiempo
const stopTimer = () => {
  clearInterval(timerInterval)
}

//Rellenar el tablero
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

//Obtener la clase para añdadir los bordes en el tablero
const getClass = (rowIndex, colIndex) => {
  if ((rowIndex + 1) % 3 === 0 && (colIndex + 1) % 3 === 0 && rowIndex !== 8 && colIndex !== 8) return 'bottom-border right-border'
  if ((colIndex + 1) % 3 === 0 && colIndex !== 8) return 'right-border'
  if ((rowIndex + 1) % 3 === 0 && rowIndex !== 8) return 'bottom-border'
}

// Verificar fuplicados
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
// Comprobar cuadrado
const checkSquare = (rowIndex, colIndex)=> {
  console.log(rowIndex,colIndex)
  rowIndex = Math.floor(rowIndex / 3) * 3;
  colIndex = Math.floor(colIndex / 3) * 3;
  let isValid = true
  const numbers = new Set()
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      const value = board.value[rowIndex+i][colIndex+j].value
      if (value < 1 || value > 9) {
        isValid = false
      }

      if (numbers.has(value)) {
        isValid =  false
      }

      if (value !== 0) {
        numbers.add(value)
      }
    }
  }
  return isValid
}
// Comprobar el juego
const checkGame = () => {
  for (let row = 0; row < 9; row++) {
    for (let col = 0; col < 9; col++) {
      if (duplicateRowCol(row, col, board.value[row][col].value) || !checkSquare(row, col)) {
        gameWin.value = 'lose'
        return
      }
    }
  }
  gameWin.value = 'win'
}

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
}
.buttons{
  margin-top: 1rem;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}
.buttons button{
  border-radius: 0.2rem;
  border: 1px solid #d5d7dd;
  padding:4px 6px;
  background-color: #d5d7dd;
  transition: all;
}
.buttons button:hover{
  cursor:pointer;
  background-color: #a9afc0;
  border-color: #a9afc0;
  transition: all;
}
.buttons button:active {
  transform: translateY(4px);
  transition: all;
}
</style>
