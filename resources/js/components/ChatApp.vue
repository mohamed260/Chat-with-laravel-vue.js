<template>
    <div class="chat-app">
        <conversation :contact="selectedContact" :messages="messages" @new="saveNewMessage"/>
        <contactList :contacts="contacts" @selected="startConversationWith" />
    </div>
</template>

<script>
    import Conversation from './Conversation';
    import ContactList from './ContactList';

    export default {
        props: {
            user: {
                type: Object,
                required: true,
            }
        },

        components: {
            Conversation,ContactList,
        },

        data() {
            return {
                selectedContact: null,
                messages: [],
                contacts: [],
            }
        },

        mounted() {
            Echo.private(`messages${this.user.id}`)
                .listen('NewMessage', (e) => {
                    this.hanleIncoming(e.message);
                })

            axios.get('/contact')
            .then((response) => {
                this.contacts = response.data;
            });
        },

        methods: {
            startConversationWith(contact) {
                this.updateUnreadCount(contact, true);
                axios.get(`/conversation/${contact.id}`)
                    .then((response) => {
                        this.messages = response.data;
                        this.selectedContact = contact;
                });
            },

            saveNewMessage(message) {
                this.messages.push(message);
            },

            hanleIncoming(message) {
                if(this.selectedContact && message.from == this.selectedContact.id){
                    this.saveNewMessage(message);
                    return;
                }

                this.updateUnreadCount(message.from_contact, false);
            },

            updateUnreadCount(contact, reset) {
                this.contacts = this.contacts.map((single) => {
                    if(single.id !== contact.id){
                        return single;
                    }

                    if(reset)
                        single.unread = 0;
                    else
                        single.unread += 1;

                    return single;
                })
            }
        },
    }
</script>

<style lang="scss" scoped>
    .chat-app{
        display: flex;
    }
</style>