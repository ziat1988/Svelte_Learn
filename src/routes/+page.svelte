<script>
    let rows = 6;
    let cols = 6;
    const numCase = rows * cols;
    const mapClass = {
        v: 'forest',
        f: 'on-fire',
        o: 'is-burned'
    }

    let grid = Array.from({ length: rows * cols }, () => 'v');
    const p = 0.8;
    let initFire = [[0,0],[4,2]]
    let countF = initFire.length

    const arrAdj = [[0,-1],[0,1],[-1,0],[1,0]]

    const delay = async (time)=> new Promise(resolve => setTimeout(resolve,time))

    async function startGame(){
        for(let arrCoord of initFire){
            const indexFire = getIndexGridByCooord(arrCoord[0], arrCoord[1])
            grid[indexFire] = 'f'
        }

        /***** start burn process ****/
        while(countF <= numCase && initFire.length > 0) {
            await delay(1000)
            const arrayF = [];
            for(let arrCoord of initFire){
                const indexFire = getIndexGridByCooord(arrCoord[0], arrCoord[1])
                for(let adj of arrAdj ){
                    const newX = adj[0] + arrCoord[0]
                    const newY = adj[1] + arrCoord[1]
                    const indexNewCell = getIndexGridByCooord(newX, newY);
                    if(newX < 0 || newY < 0 || newX > cols -1 || newY > rows - 1 || grid[indexNewCell] !== 'v') {
                        continue;
                    }


                    if(isBurnByProbability(p)){
                        grid[indexNewCell] = 'f'
                        countF ++ ;
                        arrayF.push([newX, newY])
                    }

                }

                grid[indexFire] = 'o'

            }

            initFire = arrayF
        }

    }

    startGame();

    /**
     *
     * @param{number} p
     */
    function isBurnByProbability(p)
    {
        return Math.random() <=p;
    }


    /**
     *
     * @param {int} coordX
     * @param {int} coordY
     */
    function getIndexGridByCooord(coordX, coordY)
    {
        // (2,1) => index 8
        return coordY * cols + coordX  // 1 * 6 + 2 = 8
    }



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
    {#each grid as cell}
        <div class="grid-cell {mapClass[cell]}">{cell}</div>
    {/each}
</div>

