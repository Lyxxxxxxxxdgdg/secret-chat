<script>
import { ref } from "vue";
export default {
    data() {
        return {
            randomresult: ref("请点击开始按键"),
        };
    },
    methods: {
        start() {
            this.randomresult = Math.floor(Math.random() * 100);
        },
        changemode() {
            if (document.body.style.backgroundColor === "black") {
                document.body.style.backgroundColor = "white";
                document.body.style.color = "black";
            } else {
                document.body.style.backgroundColor = "black";
                document.body.style.color = "white";
            }
        
            
        }
    },
};
</script>

<template>
    <div class="title">testapp</div>
    <div class="container" >
        <div class="start-btn" type="button" @click="start">
            <div class="starttext" style ="user-select: none;text-align: center;color: black;font-size: 15px;margin: auto 0;">开始</div>
        </div>
        <div class="content">{{ randomresult }}</div>
    </div>
    <div class="modechange">
        <div class="modetext" @click="changemode">颜色模式</div>
    </div>
</template>

<style>
.title {
    font-size: 24px;
    color: #ffffff;
    text-align: center;
}
.container {
    display: flex;
    flex-direction: column;
    height: 400px;
    background-color: #f5f5f5;
    margin-top: 2%;
    border-radius: 20px;
    padding-top: 20px;
}
.start-btn {
    width: 10%;
    height: 10%;
    background-color: #4CAF50;
    border: none;
    border-radius: 10px;
    color: #ffffff;
    font-size: 24px;
    cursor: pointer;
    margin: 0 auto;

}
.content
{
    width: 80%;
    height: 100%;
    margin: 0 auto;
    text-align: center;
}
.modechange {
    width: 10%;
    height: 10%;
    background-color: rgb(113, 65, 168);
    border: none;
    border-radius: 10px;
    color: #ffffff;
    font-size: 24px;
    cursor: cursor;

    margin-top: 20px;
}
.modetext {
    margin: 0 auto;
    text-align: center;
    font-size: xx-small;
    user-select: none;
    color: rgb(100, 65, 168);
}
</style>