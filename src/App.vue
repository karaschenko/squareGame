<template>
    <div id="app">
        <div class="container">
            <h1>Catch yellow squares to make them green</h1>
            <div class="row">
                <div class="form-group col-md-3">
                    <label for="speed">game speed (ms)</label>
                    <input type="number"
                           id="speed"
                           placeholder="Задайте интервал появления цвета"
                           v-model.number="speed"

                           class="form-control">
                </div>
                <div class="form-group col-md-3">
                    <div class="alert alert-success">
                        User score: {{userScore.length}}
                    </div>
                </div>
                <div class="form-group col-md-3">
                    <div class="alert alert-danger">
                        Computer score: {{computerScore.length}}
                    </div>
                </div>

                <div class="form-group col-md-3">
                    <button type="button"
                            class="btn btn-success"
                            @click="startGame" v-if="!isPlaying">start
                    </button>
                    <button type="button"
                            class="btn btn-success"
                            @click="stopGame"
                             v-else="isPlaying">stop
                    </button>
                </div>
            </div>
            <div class="game_status">
                <div class="alert alert-success">{{gameStatus}}</div>
            </div>
            <div class="item-list row" v-if="isPlaying">
                <div class="item"
                     @click="userAction"
                     :class="{current: item === current, user: userScore.indexOf(item) != -1, computer: computerScore.indexOf(item) != -1}"
                     :id="item"
                     v-for="item in +squares"
                     :key="item">{{item}}
                </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'app',
        data() {
            return {
                speed: 1000,
                squares: 100,
                current: null,
                userScore: [],
                computerScore: [],
                currentTimer: null,
                isPlaying: false,
                gameStatus: 'Please, start the game'
            }
        },
        methods: {
            getCurrent(arr) {
                return arr[Math.floor(Math.random() * arr.length)]
            },
            startGame() {
                this.gameStatus = 'Game is running';
                let availableSquares = Array.from({length: this.squares}, (v, k) => k + 1);
                this.isPlaying = true;
                let preIndex = -1;
                this.currentTimer = setInterval(() => {
                    if (preIndex >= 0 && this.userScore.indexOf(preIndex) === -1) {
                        this.computerScore.push(this.current);
                    }
                    this.current = this.getCurrent(availableSquares);
                    if(this.userScore.length >=  10) {
                        this.stopGame();
                        this.gameStatus = 'User has won';
                        return;
                    } else if(this.computerScore.length >=  10) {
                        this.stopGame();
                        this.gameStatus = 'Computer has won';
                        return;
                    }
                    preIndex = this.current;
                    availableSquares.splice(availableSquares.indexOf(this.current), 1);
                }, +this.speed);
            },
            stopGame() {
              this.isPlaying = false;
              this.userScore = [];
              this.computerScore = [];
              clearTimeout(this.currentTimer);
              this.current = [];

            },
            userAction(event) {
                let square = event.currentTarget;
                if (square.className.indexOf('current') !== -1) {
                    this.userScore.push(+square.id);
                    square.classList.add('user');
                }
            }
        }
    }
</script>

<style lang="sass">
    #app
        font-family: 'Avenir', Helvetica, Arial, sans-serif
        -webkit-font-smoothing: antialiased
        -moz-osx-font-smoothing: grayscale
        color: #2c3e50

        .item-list
            max-width: 600px
            margin: auto

        .form-group
            display: flex
            flex-direction: column
            justify-content: flex-end
            margin-bottom: 30px
            & .alert
                margin-bottom: 0

        .item
            flex: 1 1 10%
            border: 1px solid #000
            padding-bottom: 6%
            background: #0000ff
            color: #fff

            &.user, &.current.user
                background: #008000

            &.computer
                background: #ff0000

            &.current
                background: #ffff00

</style>


