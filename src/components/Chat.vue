<script setup>
import { ref, onMounted } from 'vue';

let message = ref('');
let messages = ref(['hello', 'world']);
let socket = null;

onMounted(() => {
    //console.log('mounted');
    //connect to web sockets webserver
    socket = new WebSocket('wss://discord-ohb2.onrender.com/primus');

    //listen for messages from web sockets webserver
    socket.onmessage = (event) => {
        //console.log(event.data);
        let newMessage = JSON.parse(event.data); // string met letters omzetten naar JSON
        console.log(event.data);
        if(newMessage.action == "newMessage"){
            messages.value.push(newMessage.message);
        }
       
    };
});


const sendMessage = () =>{
    let newMessage = {
        "message": message.value,
        "action": "newMessage"
    }

    //send message to web sockets webserver
    socket.send(JSON.stringify(newMessage));

    // send message to web sockets webserver
    message.value = "";
}
</script>

<template>
  <div>
    <ul>
      <li v-for="m in messages">{{ m }}</li>
    </ul>

    <div>   
        <input v-model="message" type="text"/>
        <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<style scoped>

</style>