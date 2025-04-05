<script>

import Constants from "./Constants.vue";

export default {
    mixins: [Constants],
    methods: {
        isValid(structure, newRow, newColumn) {
            for (let row = 0; row < structure.length; row++) {
                if (newRow + row < 0) {
                  continue;
                }

                for (let col = 0; col < structure[row].length; col++) {
                    if (structure[row][col] && (
                        newColumn + col < 0 ||
                        newColumn + col >= this.columns ||
                        newRow + row >= this.rows ||
                        this.grid[newRow + row][newColumn + col])
                    ) {
                        return false;
                    }
                }
            }

            return true;
        },
    }
}
</script>
