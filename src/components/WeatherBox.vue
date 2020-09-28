<template>
        <div class="weather-box" 
            @mouseenter="location.revealed = true"
            @mouseleave="location.revealed = false"
            :style="{
                'background-image': 'url(' + image + ')',
                'color' : this.font_color,
            }"
        >
        <transition name="fade">
            <div class="options" v-if="location.revealed">
                <button @click="removeLocation">&#10799;</button>
            </div>
        </transition>
        <i v-if="!image">#{{ location.data.weather[0].id }}</i>
            <div class="header">
                <div class="temp">
                    {{ Math.round(location.data.main.temp) }}&deg;
                </div>
                <div class="location">
                    {{ location.data.name }}
                    <transition name="fade">
                        <span v-if="location.revealed"><br/>{{ location.data.sys.country }}</span>
                    </transition>
                </div>
            </div>
            <transition name="fade">
                <div class="body" v-if="location.revealed">
                    <div class="status">
                        {{ location.data.weather[0].description }}
                    </div>
                    <table>
                        <tr>
                            <td id="left">Wilgotność</td>
                            <td id="right">{{location.data.main.humidity}}%</td>
                        </tr>
                        <tr>
                            <td id="left">Ciśnienie</td>
                            <td id="right">{{location.data.main.pressure}} hPa</td>
                        </tr>
                        <tr>
                            <td id="left">Widoczność</td>
                            <td id="right">{{location.data.visibility}}</td>
                        </tr>
                        <tr>
                            <td id="left">Wschód słońca</td>
                            <td id="right">{{unixToTime(location.data.sys.sunrise, location.data.timezone)}}</td>
                        </tr>
                        <tr>
                            <td id="left">Zachód słońca</td>
                            <td id="right">{{unixToTime(location.data.sys.sunset, location.data.timezone)}}</td>
                        </tr>
                        <tr>
                            <td id="left">Strefa czasowa</td>
                            <td id="right">UTC {{(location.data.timezone / 3600) - 1}}</td>
                        </tr>
                    </table>
                </div>
            </transition>
        </div>
</template>

<script>
export default {
    props: ['location', 'weatherCodes'],
    data(){
        return{
            weather: "",
            image: "",
            font_color: "",
        }
    },
    mounted(){
        this.weather = {
            type: this.location.data.weather[0].id,
            daytime: this.location.data.weather[0].icon[2]
        }
        try {
            // ehh...
            if(this.weather.daytime == "n"){
                // NIGHT
                this.image = require("../assets/" + 
                    this.weatherCodes[this.weather.type].night_image
                    )
                this.font_color = this.weatherCodes[this.weather.type].night_color
            } else {
                // DAY
                this.image = require("../assets/" + 
                    this.weatherCodes[this.weather.type].day_image
                    )
                this.font_color = this.weatherCodes[this.weather.type].day_color
            }
        } catch (error) {
            console.warn(error)
        }
    },
    methods:{
        removeLocation(){
            this.$emit('removeLocation', this.location.id)
        },
        unixToTime(unix, timezone){
            var hours = new Date((unix + timezone) * 1000).getUTCHours()
            var minutes = new Date((unix + timezone) * 1000).getUTCMinutes()
            return hours + ":" + minutes
        }
    }
};
</script>

<style lang="scss">
.fade-enter-active, .fade-leave-active {
  transition: opacity .25s;
}
.fade-enter-active{
    transition-delay: .25s;
}
.fade-leave-active{
    transition-delay: 0;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.weather-box{
    overflow: hidden;
    -webkit-transition:  .25s ease-in-out;
    -moz-transition: .25s ease-in-out;
    -o-transition: .25s ease-in-out;
    transition: .25s ease-in-out;
    transition-property: max-width;
    height: calc(450px);
    width: 300px;
    max-width: 150px;
    padding: 0;
    margin: 0 .5rem;
    background: grey;
    background-image: url('~@/assets/800n.jpg');
    background-repeat: no-repeat;
    background-size: auto 100%;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    button{
        border: 1px solid transparent;
        color: inherit;
        background: transparent;
        width: 32px;
        height: 32px;
        transition: .25s all cubic-bezier(0.19, 1, 0.22, 1);
        &:hover{
            background: rgb(209, 66, 66);
            color: #eeeeee;
            border: 1px solid #eeeeee;
            cursor: pointer;
        }
    }
    .header{
        margin-top: 3rem;
        text-align: center;
        .temp{
            font-size: 46px;
        }
        .location{
            font-size: 16px;
            line-height: 16px;
        }
    }
    .body{
        text-align: center;
        color: inherit;
        font-size: 12px;
        table{
            width: calc(100% - 2rem);
            margin: 0 1rem;
            tr{
                display: grid;
                grid-template-columns: 1fr 50px;
                // border-bottom: 1px solid rgba(220, 220, 220, 0.5);
                td#left{
                    text-align: left;
                }
                td#right{
                    text-align: right;
                }
            }
        }
    }
    .options{
        padding: .25rem;
        position: absolute;
    }
    &:hover{
        max-width: 300px;
        cursor: pointer;
    }
}
</style>