<template>
        <div class="weather-box" 
            @mouseenter="place.expandDetails = true"
            @mouseleave="place.expandDetails = false"
            :style="{
                'background-image': 'url(' + image + ')'
            }"
        >
        <transition name="fade">
            <div class="options" v-if="place.expandDetails">
                <button @click="removeWeather">&#10799;</button>
            </div>
        </transition>
        <i v-if="!image">#{{ place.data.weather[0].id }}</i>
            <div class="header">
                <div class="temp">
                    {{ Math.round(place.data.main.temp) }}&deg;
                </div>
                <div class="location">
                    {{ place.data.name }}
                </div>
            </div>
            <transition name="fade">
                <div class="body" v-if="place.expandDetails">
                    <div class="status">
                        {{ place.data.weather[0].description }}
                    </div>
                    <table>
                        <tr>
                            <td id="left">Wilgotność</td>
                            <td id="right">{{place.data.main.humidity}}%</td>
                        </tr>
                        <tr>
                            <td id="left">Ciśnienie</td>
                            <td id="right">{{place.data.main.pressure}} hPa</td>
                        </tr>
                        <tr>
                            <td id="left">Widoczność</td>
                            <td id="right">{{place.data.visibility}}</td>
                        </tr>
                        <tr>
                            <td id="left">Wschód słońca</td>
                            <td id="right">{{unixToTime(place.data.sys.sunrise, place.data.timezone)}}</td>
                        </tr>
                        <tr>
                            <td id="left">Zachód słońca</td>
                            <td id="right">{{unixToTime(place.data.sys.sunset, place.data.timezone)}}</td>
                        </tr>
                        <tr>
                            <td id="left">Strefa czasowa</td>
                            <td id="right">UTC {{(place.data.timezone / 3600) - 1}}</td>
                        </tr>
                    </table>
                </div>
            </transition>
        </div>
</template>

<script>
export default {
    props: ['place'],
    data(){
        return{
            image: require("../assets/" + this.place.data.weather[0].id + this.place.data.weather[0].icon[2] + '.jpg')
        }
    },
    methods:{
        removeWeather(){
            this.$emit('removeWeather', this.place.id)
        },
        unixToTime(unix, timezone){
            var hours = new Date((unix + timezone) * 1000).getUTCHours()
            var minutes = new Date((unix + timezone) * 1000).getUTCMinutes()
            return hours + ":" + minutes
        },
    }
};
</script>

<style lang="scss">
.fade-enter-active, .fade-leave-active {
  transition: opacity .25s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
button{
    border: 1px solid transparent;
    color: white;
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
    background-image: url('~@/assets/800n.jpg');
    background-repeat: no-repeat;
    background-size: auto 100%;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);

    .header{
        margin-top: 3rem;
        text-align: center;
        .temp{
            font-size: 46px;
        }
        .location{
            font-size: 16px;
        }
    }
    .body{
        text-align: center;
        color: gainsboro;
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