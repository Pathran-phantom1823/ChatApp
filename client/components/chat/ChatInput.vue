<template>
    <div class="chat__input">
        <textarea
            class="chat__input-control"
            placeholder="Type a Message"
            v-model.trim="content"
            v-on:keyup="triggerMessageSend"
            data-gramm_editor="false"
        ></textarea>
        <button class="btn  btn--info m-0" @click="sendMessage">Send</button>
    </div>
</template>


<script>
import { mapGetters } from 'vuex';

export default {
    name: 'ChatInput',
    data: function() {
        return {
            content: ''
        };
    },
    computed: {
        ...mapGetters(['getUserData', 'getCurrentRoom', 'getSocket'])
    },
    methods: {
        sendUserTyping() {
            this.getSocket.emit('userTyping', {
                room: this.getCurrentRoom,
                user: this.getUserData
            });
        },
        sendUserNotTyping() {
            this.getSocket.emit('removeUserTyping', {
                room: this.getCurrentRoom,
                user: this.getUserData
            });
        },
        triggerMessageSend(e) {
            e.preventDefault();
            if (e.keyCode === 13 && !e.shiftKey) {
                this.sendMessage();
            } else {
                if (this.content !== '') {
                    this.sendUserTyping();
                } else {
                    this.sendUserNotTyping();
                }
            }
        },
        sendMessage() {
            this.getSocket.emit('newMessage', {
                room: this.getCurrentRoom,
                user: this.getUserData,
                content: this.content
            });
            this.content = '';
            this.sendUserNotTyping();
        }
    },
    mounted() {}
};
</script>


<style lang="scss" scoped>
@import '@/assets/scss/views/chat.scss';
// .chat__input{
//      box-shadow: 0 0 1rem 0 rgba(0, 0, 0, .2);   
//         border-radius: 5px;
//         background-color: rgba(252, 253, 253, 0.808);
        
//         backdrop-filter: blur(5px);
// }
</style>
