<script>
    import Actions from "./mixins/Actions.vue";

    export default {
        name: "Tetris",
        mixins: [
            Actions,
        ],
        data() {
            return {
                frame: null,
                playText: 'Play',
            }
        },
        methods: {
            async play() {
                const {width, height} = this.ctx.canvas;

                this.active = true;
                this.score = 0;
                this.lines = 0;
                this.level = 1;
                this.playText = 'Restart'
                this.setTickRate();

                this.ctx.clearRect(0, 0, width, height);
                this.grid = this.getEmptyBoard();

                this.nextTetromino = await this.getRandomTetromino();
                await this.setNextTetromino();

                this.frame = requestAnimationFrame(this.run);
            },
            run() {
                setTimeout(
                    () => {
                        if (this.active) {
                            this.frame = requestAnimationFrame(this.run);
                        }
                    },
                    this.timePerTick
                );

                const {width, height} = this.ctx.canvas;
                this.ctx.clearRect(0, 0, width, height);
                this.paintBoard();

                if (this.count++ > this.tickRate) {
                    this.count = 0;
                    this.moveDown();
                }

                this.paintCurrentTetromino();
            },
            getEmptyBoard() {
                return Array.from(
                    {length: this.rows},
                    () => Array(this.columns).fill(0)
                );
            },
        },
        created() {
            window.addEventListener('keydown', (e) => {
                switch (e.key) {
                    case 'ArrowLeft':
                        this.moveLeft();
                        break;
                    case 'ArrowRight':
                        this.moveRight();
                        break;
                    case 'ArrowUp':
                        this.rotate();
                        break;
                    case 'ArrowDown':
                        this.speedUp();
                }
            });

            window.addEventListener('keyup', (e) => {
                if (e.key === 'ArrowDown') {
                    this.setTickRate();
                }
            })
        },
        mounted() {
            this.canvas = document.getElementById('board');
            this.ctx =  this.canvas.getContext('2d');

            this.nextCanvas = document.getElementById('next');
            this.nextCtx = this.nextCanvas.getContext('2d')

            this.ctx.canvas.width = this.columns * this.block_size;
            this.ctx.canvas.height = this.rows * this.block_size;

            this.nextCtx.canvas.width = 6 * this.block_size;
            this.nextCtx.canvas.height = 4 * this.block_size;
        }
    }
</script>

<template>
    <div class="grid">
        <canvas id="board" class="game-board"></canvas>
        <div class="right-column">
            <div>
                <h1>TETRIS</h1>
                <p>Score: <span v-html="score"></span></p>
                <p>Lines: <span v-html="lines"></span></p>
                <p>Level: <span v-html="level"></span></p>

                <div class="canvas-header">
                    <h2>NEXT</h2>
                </div>
                <canvas id="next" class="next"></canvas>
            </div>

            <button @click="play" class="play-button" v-html="playText"></button>
        </div>
    </div>
</template>

<style scoped lang="scss">
* {
    font-family: 'Press Start 2P', 'Arial';
}

.grid {
    display: grid;
    grid-template-columns: 320px 200px;
    justify-content: center;
    padding: 30px 0;

    .right-column {
        margin-left: 10px;
        color: white;
        display: flex;
        flex-direction: column;
        justify-content: space-between;

        .play-button {
            background-color: #221c57;
            text-decoration: underline;
            color: white;
            border-radius: 6px;
            font-size: 16px;
            padding: 15px 30px;
            cursor: pointer;
        }

        p {
            padding-left: 5px;
        }

        .canvas-header,
        .next {
            border: 6px groove #a2a3a2;
            background-color: #221c57;
        }

        .canvas-header {
            width: 180px;
            text-align: center;
            border-bottom: none;
            border-radius: 4px 4px 0 0;

            h2 {
                margin-bottom: 0;
            }
        }

        .next {
            border-top: none;
            border-radius: 0 0 4px 4px;
        }
    }

    .game-board {
        border: 6px groove #a2a3a2;
        border-radius: 4px;
        background-color: #221c57;
    }
}
</style>
