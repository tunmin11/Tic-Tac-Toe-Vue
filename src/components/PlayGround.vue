<template>
<div class="playground">
    <div class="grid grid-cols-3 mt-24 w-1/6 p-4 mx-auto ">
        <div @click="fillCell(0,0)" class="w-full border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[0][0] }} </div>
        <div @click="fillCell(0,1)" class="w-full border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[0][1] }} </div>
        <div @click="fillCell(0,2)" class="w-full border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[0][2] }} </div>
      
        <div @click="fillCell(1,0)" class="w-full border-t-2 border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[1][0] }} </div>
        <div @click="fillCell(1,1)" class="w-full border-t-2 border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[1][1] }} </div>
        <div @click="fillCell(1,2)" class="w-full border-t-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[1][2] }} </div>
      
        <div @click="fillCell(2,0)" class="w-full border-t-2 border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[2][0] }} </div>
        <div @click="fillCell(2,1)" class="w-full border-t-2 border-r-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[2][1] }} </div>
        <div @click="fillCell(2,2)" class="w-full border-t-2 border-white text-white text-xl py-6 font-weight-bold h-20 "> {{ game_grid[2][2] }} </div>
    </div>
    <div class="py-8" v-if="!gamestart">
        <div>
            <button @click="startGame('X')" class="py-3 px-10 bg-red-200 hover:bg-red-500 transform duration-1000 text-white font-bold text-3xl">X</button>
            <button @click="startGame('O')" class="py-3 px-10 bg-blue-200 hover:bg-blue-500 transform duration-1000 text-white font-bold text-3xl">O</button>
        </div>
    </div>
    <div v-if="gameover" class="pt-11 text-slate-50">
        <div class="text-xl " v-if="winner != ''">
             The Winner is : {{ winner }}
        </div>
        <div class="text-lg" v-if="isDraw">
             Game was draw.
        </div>
        <button @click="resetGame" class="tracking-wider mt-9 px-3 py-2 bg-yellow-300 hover:bg-yellow-500 text-gray-700">
            RESTART
        </button>   
    </div>
</div>
</template>

<style>
    .playground{
      font-family: 'Gloria Hallelujah', cursive;
    }
</style>

<script>
export default {
    data()
    {
        return {
            current_turn : 'X',
            game_grid : [
                [ null , null, null ],
                [ null , null, null ],
                [ null , null, null ],
            ],
            _game_template : [
                [ null , null, null ],
                [ null , null, null ],
                [ null , null, null ],
            ],
            winning_patterns : [
                [ [0,0], [0,1], [0,2] ],
                [ [1,0], [1,1], [1,2] ],
                [ [2,0], [2,1], [2,2] ],
                [ [0,0], [1,0], [2,0] ],
                [ [0,1], [1,1], [2,1] ],
                [ [0,2], [1,2], [2,2] ],
                [ [0,0], [1,1], [2,2] ],
                [ [0,2], [1,1], [2,0] ],
            ],
            gameover : false,
            gamestart : false,
            winner : '',
            isDraw : false,
        }
    },
    methods: {
        startGame(player)
        {
            this.current_turn = player;
            this.gamestart = true;
        },
        resetGame()
        {
            this.game_grid = JSON.parse(JSON.stringify(this._game_template));
            this.gameover = false;
            this.isDraw = false;
            this.winner = '';
        },
        fillCell(x,y)
        {
            if(!this.gameover && this.gamestart)
            {
                if(this.game_grid[x][y] == null)
                {
                    this.game_grid[x][y] = this.current_turn;
                    if(this.isWinning())
                    {
                        this.winner = this.current_turn;
                        this.gameover = true;
                    }
                    else
                    {
                        this.current_turn = (this.current_turn == 'X') ? 'O' : 'X'; 
                    }
                    
                    if(this.isAllFilled(this.game_grid) && !this.isWinning())
                    {
                        this.gameover = true;
                        this.isDraw = true;
                    }

                }
            }
        },
        isWinning()
        {
            let current_data = this.game_grid;
            for(let w_p of this.winning_patterns)
            {
                let data_set = [ ];
                w_p.map( p => {
                    data_set.push(current_data[p[0]][p[1]]);
                })
                
                if(this.isAllEqual(data_set) && !this.isAllNull(data_set))
                {
                    return true;
                }
            }

        },
        isAllEqual: arr => arr.every(val => val === arr[0] ),
        isAllNull: arr => arr.every( val => val === null ),
        isAllHasValue: arr => arr.every( val => val !== null ),
        isAllFilled(arr)
        {
            let s_arr = [];
            for( let a of arr )
            {
                s_arr.push(this.isAllHasValue(a));
            }

            return s_arr.every( i => i === true );
        }
    }
}
</script>