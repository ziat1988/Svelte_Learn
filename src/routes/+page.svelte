<script>
    import { writable } from 'svelte/store';

    class Game
    {
        /**
         *
         * @param {int} rows
         * @param {int} cols
         * @param {array} initFires
         * @param {number} p
         */
        constructor(rows, cols, initFires, p) {
            this.rows = rows;
            this.cols= cols;
            this.initFires = initFires;
            this.p = p;

            this.grid = Array.from({ length: rows * cols }, () => 'v');
            this.grid$ = writable(this.grid)
        }

        async start()
        {
            let countF = this.initFires.length
            const numCase = this.cols * this.rows;
            const arrAdj = [[0,-1],[0,1],[-1,0],[1,0]]

            while(countF <= numCase && this.initFires.length > 0) {

                await this.delay(1000)
                const arrayF = [];
                for(let arrCoord of this.initFires){
                    const indexFire = this.getIndexGridByCooord(arrCoord[0], arrCoord[1])
                    for(let adj of arrAdj ){
                        const newX = adj[0] + arrCoord[0]
                        const newY = adj[1] + arrCoord[1]
                        const indexNewCell = this.getIndexGridByCooord(newX, newY);
                        if(newX < 0 || newY < 0 || newX > this.cols -1 || newY > this.rows - 1 || this.grid[indexNewCell] !== 'v') {
                            continue;
                        }


                        if(this.isBurnByProbability(this.p)){
                            this.grid[indexNewCell] = 'f'
                            this.grid$.set(this.grid)
                            countF ++ ;
                            arrayF.push([newX, newY])
                        }

                    }

                    this.grid[indexFire] = 'o'
                    this.grid$.set(this.grid)

                }

                this.initFires = arrayF
            }

        }


        initDisplay()
        {
            for(let arrCoord of this.initFires){
                const indexFire = this.getIndexGridByCooord(arrCoord[0], arrCoord[1])
                this.grid[indexFire] = 'f'
            }
        }

        async delay(time) {
            return new Promise(resolve => setTimeout(resolve, time));
        }

        /**
         *
         * @param{number} p
         */
        isBurnByProbability(p)
        {
            return Math.random() <=p;
        }


        /**
         *
         * @param {int} coordX
         * @param {int} coordY
         */
        getIndexGridByCooord(coordX, coordY)
        {
            // (2,1) => index 8
            return coordY * this.cols + coordX  // 1 * 6 + 2 = 8
        }

        /**
         *
         * @param{string} cell
         */
        mapClass(cell)
        {
            const mapClass = {
                v: 'forest',
                f: 'on-fire',
                o: 'is-burned'
            }

            return mapClass[cell];
        }

    }

    const game = new Game(6,6,[[0,0],[4,2]], 0.3)
    game.initDisplay()
    game.start()
    const gridStore = game.grid$
</script>

<style>

    .is-burned{
        background-color: darkgray;
    }
    .on-fire{
        background-color: indianred;
    }
    .forest{
        background-color: lightgreen;
    }
    .grid-container {
        display: grid;
        grid-template-columns: repeat(6, 1fr);
        grid-template-rows: repeat(6, 1fr);
        gap: 10px; /* Adjust the gap between cells */
        width: 300px; /* Adjust the width of the grid */
        height: 300px; /* Adjust the height of the grid */
    }

    .grid-cell {
        /*background-color: lightgray;*/
        border: 1px solid ;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 1.5rem;
    }
</style>

<div class="grid-container">
    {#each $gridStore as cell}
        <div class="grid-cell {game.mapClass(cell)}">{cell}</div>
    {/each}
</div>

