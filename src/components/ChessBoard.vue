<template>
  <div class="chess-game">
    <h1>Vue Chess Game</h1>
    <div class="board">
      <div
        v-for="(square, index) in board"
        :key="index"
        :class="[
          'square',
          square.color,
          { selected: selectedSquare === index },
        ]"
        @click="handleSquareClick(index)"
      >
        <span v-if="square.piece" :class="['piece', square.piece.color]">
          {{ square.piece.type }}
        </span>
      </div>
    </div>
    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      board: [],
      selectedSquare: null,
      currentPlayer: "white",
      message: "",
    };
  },
  created() {
    this.initializeBoard();
  },
  methods: {
    initializeBoard() {
      // Initialize an 8x8 board
      this.board = Array(64)
        .fill(null)
        .map((_, index) => ({
          color: (Math.floor(index / 8) + index) % 2 === 0 ? "white" : "black",
          piece: null,
        }));

      // Place initial pieces
      const initialPositions = [
        // Rooks, knights, bishops, queen, king
        ["R", "N", "B", "Q", "K", "B", "N", "R"],
        Array(8).fill("P"), // Pawns
      ];

      // Add white pieces
      initialPositions.forEach((row, rowIndex) => {
        row.forEach((type, colIndex) => {
          this.board[rowIndex * 8 + colIndex].piece = { type, color: "white" };
        });
      });

      // Add black pieces
      initialPositions.forEach((row, rowIndex) => {
        row.forEach((type, colIndex) => {
          this.board[63 - rowIndex * 8 - colIndex].piece = {
            type,
            color: "black",
          };
        });
      });
    },
    handleSquareClick(index) {
      const square = this.board[index];

      // If a square with a piece is clicked, select it
      if (square.piece && square.piece.color === this.currentPlayer) {
        this.selectedSquare = index;
        this.message = `Selected ${square.piece.type} at ${this.getPosition(
          index
        )}`;
        return;
      }

      // If a piece is selected, attempt to move
      if (this.selectedSquare !== null) {
        this.movePiece(index);
      }
    },
    movePiece(targetIndex) {
      const sourceSquare = this.board[this.selectedSquare];
      const targetSquare = this.board[targetIndex];

      // Simple move validation (ignoring specific piece rules for simplicity)
      if (
        targetSquare.piece &&
        targetSquare.piece.color === this.currentPlayer
      ) {
        this.message = "Cannot capture your own piece.";
        return;
      }

      // Move the piece
      targetSquare.piece = sourceSquare.piece;
      sourceSquare.piece = null;

      this.selectedSquare = null;

      // Switch turns
      this.currentPlayer = this.currentPlayer === "white" ? "black" : "white";
      this.message = `${this.currentPlayer}'s turn.`;
    },
    getPosition(index) {
      const row = 8 - Math.floor(index / 8);
      const col = String.fromCharCode(97 + (index % 8));
      return `${col}${row}`;
    },
  },
};
</script>

<style scoped>
.chess-game {
  text-align: center;
  font-family: Arial, sans-serif;
}
.board {
  display: grid;
  grid-template-columns: repeat(8, 50px);
  margin: 20px auto;
  border: 2px solid #444;
}
.square {
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  cursor: pointer;
  position: relative;
}
.square.white {
  background-color: #f0d9b5;
}
.square.black {
  background-color: #b58863;
}
.square.selected {
  outline: 3px solid yellow;
}
.piece {
  font-weight: bold;
  text-transform: uppercase;
}
.piece.white {
  color: white;
}
.piece.black {
  color: black;
}
</style>
