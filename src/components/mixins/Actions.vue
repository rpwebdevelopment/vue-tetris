<script>

import Tetrominos from "./Tetrominos.vue";
import Rules from "./Rules.vue";

export default {
    mixins: [
        Tetrominos,
        Rules,
    ],
    data() {
        return {
            canvas: null,
            nextCanvas: null,
            ctx: null,
            nextCtx: null,
            count: 0,
            grid: null,
            currentTetromino: null,
            nextTetromino: null,
        }
    },
    methods: {
        async addScore(rows) {
            let modifier;

            if (!rows) {
                return;
            }

            switch (rows) {
                case 1:
                  modifier = 100;
                    break;
                case 2:
                  modifier = 300;
                    break;
                case 3:
                    modifier = 500;
                    break;
                case 4:
                default:
                    modifier = 800;
            }

            this.score += this.level * modifier;

            return true;
        },
        setTickRate() {
            this.tickRate = this.timeToMove / this.timePerTick;
        },
        async setNextTetromino() {
            this.currentTetromino = this.nextTetromino;
            this.nextTetromino = await this.getRandomTetromino();

            return await this.paintNextTetromino();
        },
        async gameOver() {
            cancelAnimationFrame(this.frame);

            this.active = false;

            this.ctx.fillStyle = 'black';
            this.ctx.globalAlpha = 0.60;
            this.ctx.fillRect(0, this.canvas.height / 2 - 40, this.canvas.width, 80);

            this.ctx.globalAlpha = 1;
            this.ctx.fillStyle = 'white';
            this.ctx.font = '20px "Press Start 2P"';
            this.ctx.textAlign = 'center';
            this.ctx.textBaseline = 'middle';
            this.ctx.fillText('GAME OVER!', this.canvas.width / 2, this.canvas.height / 2);
        },
        clearComplete(row) {
            this.lines++;
            if (this.lines % 10 === 0) {
               this.level++;
               this.setTickRate();
            }

            for (row; row >= 0; row--) {
                for (let cell = 0; cell < this.columns; cell++) {
                    this.grid[row][cell] = (row === 0) ? 0 : this.grid[row - 1][cell];
                }
            }
        },
        async scanForComplete() {
            let complete = 0;

            for (let row = this.rows - 1; row >= 0;) {
                if (this.grid[row].every(item => !!item)) {
                    complete++;
                    this.clearComplete(row)
                } else {
                    row--;
                }
            }

            await this.addScore(complete);

            return true;
        },
        async place() {
            let current = this.currentTetromino,
                struct = current.struct;

            for (let row = 0; row < struct.length; row++) {
                for (let col = 0; col < struct[row].length; col++) {
                    if (struct[row][col]) {
                        if (current.currentRow + row < 0) {
                            return await this.gameOver();
                        }

                        this.grid[current.currentRow + row][current.column + col] = current.name;
                    }
                }
            }
        },
        move(structure, newRow, newColumn) {
            if (this.isValid(structure, newRow, newColumn)) {
                this.currentTetromino.struct = structure;
                this.currentTetromino.column = newColumn;
                this.currentTetromino.currentRow = newRow;

                return true;
            }

            return false;
        },
        moveDown() {
            const structure = this.currentTetromino.struct,
                newRow = this.currentTetromino.currentRow + 1,
                newColumn = this.currentTetromino.column;

            if (!this.move(structure, newRow, newColumn)) {
                this.place()
                    .then(() => this.scanForComplete()
                        .then(() => this.setNextTetromino())
                    );
            }
        },
        moveLeft() {
            const structure = this.currentTetromino.struct,
                newRow = this.currentTetromino.currentRow,
                newColumn = this.currentTetromino.column - 1;

            this.move(structure, newRow, newColumn);
        },
        moveRight() {
            const structure = this.currentTetromino.struct,
                newRow = this.currentTetromino.currentRow,
                newColumn = this.currentTetromino.column + 1;

            this.move(structure, newRow, newColumn);
        },
        rotate() {
            const structure = this.currentTetromino.struct,
                newRow = this.currentTetromino.currentRow,
                newColumn = this.currentTetromino.column,
                length = structure.length - 1,
                newStructure = structure.map((row, rowIndex) =>
                    row.map((field, fieldIndex) => structure[length - fieldIndex][rowIndex])
                );

            this.move(newStructure, newRow, newColumn);
        },
        speedUp() {
            this.tickRate = 0;
        },
        paintBlock(context, col, row, fillColour, lineColour) {
            context.strokeStyle = lineColour;
            context.fillStyle = fillColour;
            context.lineWidth = 3;

            context.strokeRect(
                (col * this.block_size) + 1,
                (row * this.block_size) + 1,
                this.block_size - 4,
                this.block_size - 4
            );

            context.fillRect(
                (col * this.block_size) + 3,
                (row * this.block_size) + 3,
                this.block_size - 8,
                this.block_size - 8
            );
        },
        paintBoard() {
            for (let row = 0; row < this.rows; row++) {
                for (let col = 0; col < this.columns; col++) {
                    if (this.grid[row][col]) {
                        let tetromino = this.tetrominos[this.grid[row][col]];

                        this.paintBlock(this.ctx, col, row, tetromino.colour, tetromino.lineColour);
                    }
                }
            }
        },
        async paintTetromino(context, tetromino) {
            for (let row = 0; row < tetromino.struct.length; row++) {
                for (let col = 0; col < tetromino.struct[row].length; col++) {
                    if (tetromino.struct[row][col]) {
                        this.paintBlock(
                            context,
                            (tetromino.column + col),
                            (tetromino.currentRow + row),
                            tetromino.colour,
                            tetromino.lineColour
                        );
                    }
                }
            }

            return true;
        },
        async paintCurrentTetromino() {
            return await this.paintTetromino(this.ctx, this.currentTetromino);
        },
        async paintNextTetromino() {
            let tetronimo = Object.create(this.nextTetromino);

            this.nextCtx.clearRect(0, 0, this.nextCtx.canvas.width, this.nextCtx.canvas.height);

            tetronimo.currentRow = this.nextTetromino.nextRow;
            tetronimo.column = this.nextTetromino.nextCol;

            return await this.paintTetromino(this.nextCtx, tetronimo);
        },
        async getRandomTetromino() {
            let key = this.keys[Math.floor(Math.random() * this.keys.length)];

            return Object.create(this.tetrominos[key]);
        }
    },
    computed: {
        timePerTick() {
            return 1000 / this.fps;
        },
        timeToMove() {
            return (0.8 - ((this.level - 1) * 0.007)) * 1000;
        },
    }
}
</script>
