<script>
export default {
    data() {
        return {
            keys: ['I', 'J', 'L', 'O', 'T', 'Z'],
            tetrominos: {
                I: {
                    name: 'I',
                    colour: '#00e2e0',
                    lineColour: '#0bbfbd',
                    currentRow: -1,
                    column: 3,
                    nextCol: 1,
                    nextRow: 0.5,
                    struct: [
                        [0, 0, 0, 0],
                        [1, 1, 1, 1],
                        [0, 0, 0, 0],
                        [0, 0, 0, 0],
                    ],
                },
                J: {
                    name: 'J',
                    colour: '#014ee2',
                    lineColour: '#0242ba',
                    currentRow: -2,
                    column: 3,
                    nextCol: 1.5,
                    nextRow: 1,
                    struct: [
                        [1, 0, 0],
                        [1, 1, 1],
                        [0, 0, 0],
                    ],
                },
                L: {
                    name: 'L',
                    colour: '#e36200',
                    lineColour: '#b24100',
                    currentRow: -2,
                    column: 3,
                    nextCol: 1.5,
                    nextRow: 1,
                    struct: [
                        [0, 0, 1],
                        [1, 1, 1],
                        [0, 0, 0],
                    ],
                },
                O: {
                    name: 'O',
                    colour: '#e4de01',
                    lineColour: '#b7ac04',
                    currentRow: -2,
                    column: 4,
                    nextCol: 2,
                    nextRow: 1,
                    struct: [
                        [1, 1],
                        [1, 1],
                    ],
                },
                T: {
                    name: 'T',
                    colour: '#9b11e2',
                    lineColour: '#7902af',
                    currentRow: -2,
                    nextCol: 1.5,
                    nextRow: 1,
                    column: 3,
                    struct: [
                        [0, 1, 0],
                        [1, 1, 1],
                        [0, 0, 0],
                    ],
                },
                Z: {
                    name: 'Z',
                    colour: '#e40029',
                    lineColour: '#b3011f',
                    currentRow: -2,
                    column: 3,
                    nextCol: 1.5,
                    nextRow: 1,
                    struct: [
                        [1, 1, 0],
                        [0, 1, 1],
                        [0, 0, 0],
                    ],
                }
            }
        }
    },
}
</script>
