<template>
  <div 
    v-if="board.length > 0"
    class="board"
    :class="{ shake: gameOver }"
    :style="{ gridTemplateColumns: `repeat(${width}, 36px)` }"
  >
    <Cell
      v-for="(cell, index) in board.flat()"
      :key="`cell-${index}`"
      :value="cell.value"
      :revealed="cell.revealed"
      :flagged="cell.flagged"
      :mine="cell.mine"
      :x="index % width"
      :y="Math.floor(index / width)"
      @reveal="revealCell"
      @flag="flagCell"
    />
  </div>
</template>

<script>
import Cell from './Cell.vue';

export default {
  components: { Cell },
  props: ['difficulty', 'onFirstClick', 'onStopTimer'],
  emits: ['update:flagCount'],
  data() {
    return {
      width: 10,
      height: 10,
      mineCount: 10,
      board: [],
      hasStarted: false,
      gameOver: false 
    };
  },
  watch: {
    difficulty: {
      immediate: true,
      handler(newLevel) {
        this.setDifficultyParams(newLevel);
        this.initBoard();
      }
    }
  },
  methods: {
    checkWin() {
      if (this.gameOver) return;

      const allCells = this.board.flat();
      const nonMineCells = allCells.filter(cell => !cell.mine);
      const allRevealed = nonMineCells.every(cell => cell.revealed);

      if (allRevealed) {
        alert("You Win!");
        if (this.onStopTimer) this.onStopTimer();
        for (let row of this.board) {
          for (let c of row) {
            c.revealed = true;
          }
        }
      }
    },

    setDifficultyParams(level) {
      switch (level) {
        case 'sedang':
          this.width = 16;
          this.height = 16;
          this.mineCount = 40;
          break;
        case 'sulit':
          this.width = 30;
          this.height = 16;
          this.mineCount = 99;
          break;
        default:
          this.width = 10;
          this.height = 10;
          this.mineCount = 10;
      }
    },
    emitFlagCount() {
      const count = this.board.flat().filter(cell => cell.flagged).length;
      this.$emit('update:flagCount', count);
    },
    initBoard() {
      const board = Array(this.height).fill().map(() =>
        Array(this.width).fill().map(() => ({
          value: 0,
          revealed: false,
          flagged: false,
          mine: false
        }))
      );

      let placedMines = 0;
      while (placedMines < this.mineCount) {
        const x = Math.floor(Math.random() * this.width);
        const y = Math.floor(Math.random() * this.height);
        if (!board[y][x].mine) {
          board[y][x].mine = true;
          board[y][x].value = '🐱';
          placedMines++;
        }
      }

      for (let y = 0; y < this.height; y++) {
        for (let x = 0; x < this.width; x++) {
          if (board[y][x].mine) continue;
          board[y][x].value = this.countMinesAround(board, x, y);
        }
      }

      this.board = board;
      this.emitFlagCount();
      this.hasStarted = false;
      this.gameOver = false;
    },
    countMinesAround(board, x, y) {
      let count = 0;
      for (let dy = -1; dy <= 1; dy++) {
        for (let dx = -1; dx <= 1; dx++) {
          if (dx === 0 && dy === 0) continue;
          const nx = x + dx, ny = y + dy;
          if (nx >= 0 && nx < this.width && ny >= 0 && ny < this.height) {
            if (board[ny][nx].mine) count++;
          }
        }
      }
      return count;
    },
    revealCell(x, y) {
      if (this.gameOver) return;

  const cell = this.board[y][x];
  if (cell.revealed || cell.flagged) return;

  if (!this.hasStarted) {
    this.hasStarted = true;
    if (this.onFirstClick) this.onFirstClick();
  }

  if (cell.mine) {
    this.gameOver = true;
    if (this.onStopTimer) this.onStopTimer();
    for (let row of this.board) {
      for (let c of row) {
        c.revealed = true;
      }
    }
      alert("Game Over!");
      return;
    }

    if (cell.value === 0) {
      this.revealSurrounding(x, y);
    } else {
      cell.revealed = true;
    }

    this.checkWin();
    },
    revealSurrounding(x, y) {
      const queue = [[x, y]];
      const visited = new Set();

      while (queue.length > 0) {
        const [cx, cy] = queue.shift();
        const key = `${cx},${cy}`;
        if (visited.has(key)) continue;
        visited.add(key);

        const cell = this.board[cy][cx];
        if (cell.revealed || cell.flagged) continue;

        cell.revealed = true;

        if (cell.value === 0) {
          for (let dy = -1; dy <= 1; dy++) {
            for (let dx = -1; dx <= 1; dx++) {
              const nx = cx + dx, ny = cy + dy;
              const neighborKey = `${nx},${ny}`;
              if (
                nx >= 0 && nx < this.width &&
                ny >= 0 && ny < this.height &&
                !visited.has(neighborKey)
              ) {
                queue.push([nx, ny]);
              }
            }
          }
        }
      }
    },
    flagCell(x, y) {
      const cell = this.board[y][x];
      const totalFlags = this.board.flat().filter(cell => cell.flagged).length;

      if (!cell.revealed) {
        if (!cell.flagged && totalFlags >= this.mineCount) return; 
        cell.flagged = !cell.flagged;
        this.emitFlagCount();
      }
    }
  }
};
</script>