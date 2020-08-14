<template>
    <div class="home">
        <div class="home-wrapper">
            <h1>Simon Says</h1>
            <div class="home-content">


                <div class="simon">
                    <div class="simon-inner" ref="simonInner">
                        <div class="simon__item blue" data-value="0" @click="setActive($event)"></div>
                        <div class="simon__item red" data-value="1" @click="setActive($event)"></div>
                        <div class="simon__item yellow " data-value="2" @click="setActive($event)"></div>
                        <div class="simon__item green" data-value="3" @click="setActive($event)"></div>
                    </div>
                </div>

                <div class="round">
                    <h3> Round: {{simonClickArray.length}}</h3>
                    <div class="round-btn" @click="start">Start</div>
                    <p ref="loser"></p>
                    <div class="levels">
                        <p>
                            <label>
                                <input class="with-gap" name="group1" type="radio"   value="1500" v-model="levelSpeed"/>
                                <span>normal</span>
                            </label>
                        </p>
                        <p>
                            <label>
                                <input class="with-gap" name="group1" type="radio"  value="1000"  v-model="levelSpeed"/>
                                <span>middle</span>
                            </label>
                        </p>
                        <p>
                            <label>
                                <input class="with-gap" name="group1" type="radio"  value="400"  v-model="levelSpeed"/>
                                <span>difficult</span>
                            </label>
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'Home',
        data() {
            return {
                simonClickArray: [],
                userClickArray: [],
                countClick: -1,
                currentSimonClick: null,
                sounds:[],
                levelSpeed: 1500,
                time: 0
            }
        },
        methods: {
            start() {

                this.time= 0

                this.countClick = -1
                this.simonClickArray = []
                this.userClickArray = []
                this.currentSimonClick = this.getRandomSimonClick()
                this.simonClickArray.push(this.currentSimonClick)

                this.sounds=[]
                this.sounds.push(new Audio('./sounds/1.mp3'))
                this.sounds.push(new Audio('./sounds/2.mp3'))
                this.sounds.push(new Audio('./sounds/3.mp3'))
                this.sounds.push(new Audio('./sounds/4.mp3'))

                let items = this.$refs.simonInner.children


                this.setPointerEvent('auto')

                setTimeout(() => {
                    items.forEach((item) => {
                        if (+item.getAttribute('data-value') === this.currentSimonClick) {
                            this.sounds[this.currentSimonClick].play()
                            item.classList.add('active')
                        }
                        setTimeout(() => {
                            item.classList.remove('active')
                        }, 600)
                    })
                }, this.levelSpeed)

                this.$refs.loser.textContent = ''
            },
            getRandomSimonClick() {
                return Math.floor(Math.random() * Math.floor(4))
            },

            setActive() {

                this.countClick++
                let value = event.target.getAttribute('data-value')
                this.sounds[+value].pause()
                this.sounds[+value].currentTime = 0
                this.sounds[+value].play()


                if (this.simonClickArray.length === this.userClickArray.length) {

                    this.zeroingUserClickArray()
                    this.checkSimonClickArray()

                } else {

                    if (+value === this.simonClickArray[this.countClick]) {
                        this.userClickArray.push(+value);
                        if (this.simonClickArray.length === this.userClickArray.length) {
                            this.zeroingUserClickArray()
                            this.checkSimonClickArray()

                        }

                    } else {
                        this.$refs.loser.textContent = `Sorry, you lost after ${this.simonClickArray.length} rounds`
                        this.setPointerEvent('none')

                    }


                }


            },
            checkSimonClickArray() {
                this.simonClickArray.push(this.currentSimonClick)
                let items = this.$refs.simonInner.children
                this.time = this.levelSpeed * this.simonClickArray.length
                items.forEach((el)=>{
                    el.style.pointerEvents = 'none'
                })


                this.simonClickArray.forEach((itemClick, i = 1) => {


                    setTimeout(() => {
                        i += 1
                        items.forEach((item, index) => {


                            if (+item.getAttribute('data-value') === itemClick) {
                                item.classList.add('active')
                                this.sounds[itemClick].pause()
                                this.sounds[itemClick].currentTime = 0
                                this.sounds[itemClick].play()

                            }
                            setTimeout(() => {
                                item.classList.remove('active')

                            }, 600)

                        })



                    }, this.levelSpeed * ++i)

                    console.log(this.time)
                    setTimeout(()=>{
                        items.forEach((el)=>{
                            el.style.pointerEvents = 'auto'
                        })
                    },this.time)

                })



            },
            zeroingUserClickArray() {

                this.userClickArray = []
                this.countClick = -1
                this.currentSimonClick = this.getRandomSimonClick()
            },
            setPointerEvent(value){
                let items = this.$refs.simonInner.children
                items.forEach((item)=>{
                    item.style.pointerEvents = value
                })
            }
        },
        components: {}
        ,
    }
</script>

<style lang="scss">
    .home-wrapper {

        font-family: Arial, sans-serif;
        font-size: 15px;
        width: 100%;
        max-width: 800px;
        margin: 0 auto;
        text-align: center;

        .home-content {
            margin-top: 100px;
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            .simon {
                max-width: 300px;
                width: 100%;
                .simon-inner {
                    border-radius: 150px 150px 150px 150px;
                    overflow: hidden;
                    position: relative;
                    width:100%;

                    display: flex;
                    flex-wrap: wrap;

                    .simon__item {
                        width: 100%;
                        max-width: 150px;
                        height: 150px;
                        cursor: pointer;
                    }

                    .blue {
                        background: #42a0ff;
                        opacity: 0.5;
                        border-radius: 150px 0 0 0;
                        transition: opacity 0.2s linear;

                        &:hover {
                            border-left: 4px solid black;
                            border-top: 4px solid black;
                        }

                        &:active {
                            opacity: 1;
                        }
                    }

                    .red {
                        opacity: 0.5;
                        background: #fa7777;
                        border-radius: 0 150px 0 0;
                        transition: opacity 0.2s linear;

                        &:hover {
                            border-right: 4px solid black;
                            border-top: 4px solid black;
                        }

                        &:active {
                            opacity: 1;
                        }
                    }

                    .yellow {
                        opacity: 0.5;
                        background: #ffff6e;
                        border-radius: 0 0 0 150px;
                        transition: opacity 0.2s linear;

                        &:hover {
                            border-left: 4px solid black;
                            border-bottom: 4px solid black;
                        }

                        &:active {
                            opacity: 1;
                        }
                    }

                    .green {
                        background: #66ff66;
                        border-radius: 0 0 150px 0;
                        opacity: 0.5;
                        transition: opacity 0.2s linear;

                        &:hover {
                            border-right: 4px solid black;
                            border-bottom: 4px solid black;
                        }

                        &:active {
                            opacity: 1;
                        }
                    }

                    .active {
                        opacity: 1;
                    }
                }
            }

            .round {
                margin-top: 60px;
                text-align: center;
                padding: 0 50px;

                h3{
                    margin-bottom: 20px;
                }
                .round-btn {
                    display: inline-block;
                    padding: 10px 25px;
                    background: #42a0ff;
                    border-radius: 15px;
                    margin-bottom: 15px;
                    cursor: pointer;
                    font-size: 24px;
                    &:hover {
                        background: #77b6f6;
                    }

                }
                .levels{
                    p{
                        label{
                            margin-top: 20px;
                            display: flex;
                            justify-content: left;
                            cursor: pointer;
                            input{
                                margin-right: 10px;
                            }
                        }
                    }
                }
            }
        }
    }

    .notClickable {
        pointer-events: none;
    }
</style>