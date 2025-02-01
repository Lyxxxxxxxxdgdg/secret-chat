<script>
import { ref } from "vue";
import sha256 from 'js-sha256';
import axios from "axios";
export default {
    data() {
        return {
            randomresult: ref("请点击开始按键"),
            password: ref("5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"),
            inputPassword: ref(""),
            secret: ref(false),
            friends: [],
            selectedFriend: ref(0),
            myMessage: [],
            myTime: [],
            herMessage: [],
            herTime: [],
            inputMessage: ref(""),
            username: ref(window.localStorage.getItem("username") || "idk"),
            filter: ref(""),
            fetches: [],
        };
    },
    methods: {
        init(){
            if (window.localStorage.getItem('username')) {
                //检查密码逻辑还没写记得写
            }
            this.filter='password';
            this.fetchData(true);
            let passwords = (this.filteredItems[0]? this.filteredItems[0].value : "") .split(',');
            if(passwords.length%2!=0){
                passwords.pop();
            }
            let username = prompt("请输入用户名-未注册自动注册");
            while (username.trim() == '' ) {
                let username = prompt("用户名不能为空");
            }
            let password = prompt("请输入密码");
            while (password.trim() == '') {
                let password = prompt("密码不能为空");
            }
            if(passwords.indexOf(username)%2==0 && sha256(password) === passwords[passwords.indexOf(username)+1]){
                this.secret=true;
                this.username=username;
                window.localStorage.setItem("username", username);
            }else if(passwords.indexOf(username)%2==0 && sha256(password) != passwords[passwords.indexOf(username)+1]){
                alert("密码错误/该用户已被注册");
                return;
            }else{
                alert("该用户未注册-正在注册");
                axios.post('http://104.156.225.237:3000/add-data', { name: 'password', value: passwords.concat([username,sha256(password)]).toString()});
            }
            this.friends = window.localStorage.getItem("friends")? JSON.parse(window.localStorage.getItem("friends")) : this.friends;
            if(this.friends.length!=0)this.changefriend(this.friends[0]); 
        },
            
        
        
        start() {
            this.randomresult = Math.floor(Math.random() * 100);

        },

        changemode() {
            this.checkPassword();
            
        },
        checkPassword() {
            this.init();
        },
        changefriend(target){
            console.log(this.myTime);
            console.log(this.herTime);
            console.log(this.fetchData());
            console.log(target);
            this.selectedFriend=this.friends.indexOf(target);

            console.log(this.selectedFriend);
            this.myMessage = JSON.parse(window.localStorage.getItem(target+"myMessage"));
            this.myTime = JSON.parse(window.localStorage.getItem(target+"myTime")) ;
            this.herMessage = JSON.parse(window.localStorage.getItem(target+"herMessage")) ;
            this.herTime = JSON.parse(window.localStorage.getItem(target+"herTime")) ;
            this.syncMessage(target);
        },
        fetchData(showErr=false) {
            axios.get('http://104.156.225.237:3000/get-data')
                .then(response => {
                this.fetches = response.data;
                })
                .catch(error => {
                    if (showErr) {
                        alert('There was an error fetching the data! \n获取数据时出错' + error);
                    }
                    
                console.error('There was an error fetching the data!', error);
                });

            return this.fetches;
        },
        send() {
            let message = this.inputMessage.trim();
            if (message.trim() === '') {
                alert('请输入消息内容');
                return;
            }
            
                
                    this.myMessage.push(message);
                    this.myTime.push(Math.floor(Date.now() / 1000));
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"myMessage", JSON.stringify(this.myMessage));
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"myTime", JSON.stringify(this.myTime));
                    this.fetchData();
                    this.filter = this.username+"->"+this.friends[this.selectedFriend];
                    console.log(this.fetchData());
                    console.log(this.filteredItems);

                    console.log(((this.filteredItems[0]? this.filteredItems[0].value : "").split(',').concat(message)).toString())
                    axios.post('http://104.156.225.237:3000/add-data', { name: this.username+"->"+this.friends[this.selectedFriend], value: ((this.filteredItems[0]? this.filteredItems[0].value : "{}").split(',').concat(message)).toString()});
                    message = '';

            },
            addfriend(){
                let friendname = prompt("请输入好友名称");
                if(friendname.trim() === ''){
                    alert("请输入好友名称");
                    return;
                }
                if(this.friends.includes(friendname)){
                    alert("该好友已存在");
                    return;
                }
                this.friends.push(friendname);
                window.localStorage.setItem("friends", JSON.stringify(this.friends));
                window.localStorage.setItem(friendname+"myMessage", JSON.stringify([]));
                window.localStorage.setItem(friendname+"myTime", JSON.stringify([]));
                window.localStorage.setItem(friendname+"herMessage", JSON.stringify([]));
                window.localStorage.setItem(friendname+"herTime", JSON.stringify([]));
            },
            syncMessage(target){
                this.fetchData();
                this.filter = target+"->"+this.username;
                console.log(this.filter);
                console.log(this.filteredItems);
                console.log(this.filteredItems[0].value.split(',').slice(1));
                
                this.herMessage = this.herMessage.concat(this.filteredItems[0].value.split(',').slice(1));
                window.localStorage.setItem(this.friends[this.selectedFriend]+"herMessage", JSON.stringify(this.herMessage));
                for(let i=0;i<this.filteredItems[0].value.split(',').slice(1).length;i++){
                    this.herTime.push(Math.floor(Date.now() / 1000));
                }
                window.localStorage.setItem(this.friends[this.selectedFriend]+"herTime", JSON.stringify(this.herTime));
                let id = this.filteredItems[0].id;
                console.log(id);
                axios.delete(`http://104.156.225.237:3000/delete-data/${id}`)
            },
            deleteAll(){
                if(confirm("确定删除所有聊天记录吗？")){
                    this.myMessage = [];
                    this.myTime = [];
                    this.herMessage = [];
                    this.herTime = [];
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"myMessage", JSON.stringify([]));
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"myTime", JSON.stringify([]));
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"herMessage", JSON.stringify([]));
                    window.localStorage.setItem(this.friends[this.selectedFriend]+"herTime", JSON.stringify([]));
                    this.fetchData();
                }
            }
        
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
        filteredItems() {
            if (!this.filter) {
                return this.fetches;
            }
            return this.fetches.filter(item => 
                item.name==this.filter
            );
        },
        },
    
};

</script>

<template>
    <div class="title">随机数生成器</div>
    
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
        <div class="secretbartext">chat by nameNotFound-点击好友名字更新来自对方的消息</div>
        <div class="deleteAll" type="button" @click="deleteAll">删除聊天记录</div>
    </div>
    <div class="userchoose" v-show="secret">
        <div class="singlefriend" v-for="item in friends" type="button" @click="changefriend(item)">{{ item }}</div>
        <div class="singlefriend" type="button" @click="addfriend">+</div>
    </div>
    <div class="chatinfo" v-show="secret">{{ friends[selectedFriend] }}</div>
    <div class="chatplace" v-show="secret">
        <div v-for="(msg, index) in sortedMessages" :key="index" :class="['message', msg.isMine ? 'mymessage' : 'hermessage']">
      
            <span class="text">{{ msg.text }}</span>      
        </div>
    </div>
    <div class="sendContainer">
        <input type="text" class="chatinput" v-show="secret" placeholder="点击输入消息" v-model="inputMessage"></input>
        <div class="sendButton" type="button" v-show="secret" @click="send" ><div>发送</div></div>
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
    display: flex;
    flex-direction: row;
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
.deleteAll{
    font-size: 18px;
    color: darkslateblue;
    cursor: pointer;
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
.sendContainer{
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
}
.chatinput{
    width: 91%;
    height: 50px;
    margin: 0 auto;
    text-align: center;
    font-size: 24px;
    color: black;
    background-color: rosybrown;
    border: none;
    user-select: none;
    cursor: default;
    border: none;
    border-radius: 20px;
    margin-top: 10px;
    margin-left: 2%;
    cursor: text;
}
.sendButton{
    width: 5%;
    height: 50px;
    margin: 0 auto;
    text-align: center;
    font-size: 24px;
    color: white;
    background-color: darkslateblue;
    border: none;
    user-select: none;
    cursor: pointer;
    border: none;
    border-radius: 20px;
    margin-top: 10px;
    line-height: 50px;
}
</style>