<script>
import { ref } from "vue";
import sha256 from 'js-sha256';

export default {
    data() {
        return {
            randomresult: ref("请点击开始按键"),
            password: ref("5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"),
            inputPassword: ref(""),
            secret: ref(false),
            friends: ['f11111111111','f2'],
            selectedFriend: ref(0),
            myMessage: ['this is a test message,which is very long,so we need to make it long enough to see if it can be displayed in the chat box.testing testing testing,testing is a important thing in life.','2'],
            myTime: [1738318690,1738318695],
            herMessage: ['3','4'],
            herTime: [1738318691,1738318696],
        };
    },
    methods: {
        start() {
            this.randomresult = Math.floor(Math.random() * 100);
        },
        changemode() {
            this.checkPassword();
        },
        checkPassword() {
            if (this.password === sha256(this.inputPassword)) {
                alert("密码正确");
                this.secret=true;

            } else {
                alert("该功能正在开发");
            }
        },
        changefriend(target){
            console.log(target);
            this.selectedFriend=this.friends.indexOf(target);

            console.log(this.selectedFriend);
        },
        
    },
    computed: {
        sortedMessages() {
            let combinedMessages = [];

            // 合并我的消息
            this.myMessage.forEach((msg, index) => {
            combinedMessages.push({ text: msg, time: this.myTime[index], isMine: true });
            });

            // 合并对方的消息
            this.herMessage.forEach((msg, index) => {
                combinedMessages.push({ text: msg, time: this.herTime[index], isMine: false });
            });

            // 按时间排序
            return combinedMessages.sort((a, b) => new Date(a.time) - new Date(b.time));
        },
        },
};

</script>

<template>
    <div class="title">testapp</div>
    <input class="password" type="password" v-model="inputPassword"></input>
    <div class="container" >
        <div class="start-btn" type="button" @click="start">
            <div class="starttext" style ="user-select: none;text-align: center;color: black;font-size: 15px;margin: auto 0;">开始</div>
        </div>
        <div class="content">{{ randomresult }}</div>
    </div>
    <div class="modechange">
        <div class="modetext" @click="changemode">颜色模式</div>
    </div>
    <div class="secretbar" v-show="secret">
        <div class="secretbartext">chat</div>
    </div>
    <div class="userchoose" v-show="secret">
        <div class="singlefriend" v-for="item in friends" type="button" @click="changefriend(item)">{{ item }}</div>
    </div>
    <div class="chatinfo" v-show="secret">{{ friends[selectedFriend] }}</div>
    <div class="chatplace" v-show="secret">
        <div v-for="(msg, index) in sortedMessages" :key="index" :class="['message', msg.isMine ? 'mymessage' : 'hermessage']">
      
            <span class="text">{{ msg.text }}</span>      
        </div>
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
.password {
    width: 100%;
    height: 10%;
    margin: 0 auto;
    text-align: center;
    font-size: 24px;
    color: rgba(1, 1, 1, 0);
    background-color: rgba(1, 1, 1, 0);
    border: none;
    user-select: none;
    cursor: default;
    border: none;
    border-radius: 10px;
    margin-top: 20px;
}
.secretbar{
    width: 96%;
    height: 70px;
    border-radius: 20px;
    background-color: wheat;
    margin-top: 100%;
    margin-left: 2%;
    padding-top: 18px;
    padding-left: 25px;
    box-sizing: border-box;

}
.secretbartext{
    font-size: 24px;
    color: darkslateblue;

}
.userchoose{
    display: flex;
    flex-direction: row;
    width: 96%;
    height: 100px;
    border-radius: 20px;
    background-color: lemonchiffon;
    margin-top: 25px;
    margin-left: 2%;
    padding-top: 0px;
    padding-left: 25px;
    padding-right: 25px;
    box-sizing: border-box;
    overflow-x: auto;
    overflow-y: hidden;
}
.singlefriend{
    width: 80px;
    height: 80px;
    border-radius: 20px;
    background-color: antiquewhite;
    margin-top: 10px;
    margin-left: 2%;
    padding-top: 33px;
    box-sizing: border-box;
    text-align: center;
    user-select: none;
    overflow-x: hidden;
}
.chatinfo{
    width: 96%;
    height: 80px;
    border-radius: 20px 20px 0px 0px;
    background-color: lightblue;
    margin-top: 10px;
    margin-left: 2%;
    padding-top: 33px;
    box-sizing: border-box;
    text-align: center;
    user-select: none;
    overflow-x: hidden;
}
.chatplace{
    display: flex;
    flex-direction: column;
    width: 96%;
    height: 800px;
    border-radius: 0px 0px 20px 20px;
    background-color: lightblue;
    margin-left: 2%;
    padding-top: 0px;
    padding-left: 25px;
    padding-right: 25px;
    box-sizing: border-box;
    overflow-x: auto;
    overflow-y: auto;
}
.hermessage{
    display: table;
    width: 50px;
    height: 35px;
    border-radius: 20px 20px 20px 0px;
    background-color: white;
    margin-top: 10px;
    margin-left: 2%;
    padding: 5px;
    box-sizing: border-box;
    text-align: center;
    overflow: hidden;
    float : left;
}
.mymessage{
    display: table;
    width: 50px;
    height: 35px;
    border-radius: 20px 20px 0px 20px;
    background-color: white;
    margin-top: 10px;
    margin-left: auto; 
    padding: 5px;
    box-sizing: border-box;
    text-align: center;
    overflow: hidden;
    
}
</style>