<template>
  <div class="composer">
      <form @click="send" method="POST">
        <div class="fox">
            <textarea v-model="message" @keydown.enter="send" placeholder="Message . . ."></textarea>
            
            <button class="btn btn-success">Sent</button>
            
        </div>
      </form>
  </div>
</template>

<script>
export default {
    data() {
        return {
            message: ''
        }
    },

    methods: {
        send(e) {
            e.preventDefault();

            if(this.message == ''){
                return;
            }

            this.$emit('send', this.message);
            this.message = '';
        }
    }
}
</script>

<style lang="scss" scoped>
    .composer textarea {
        width: 80%;
        margin: 10px;
        resize: none;
        border-radius: 3px;
        border: 1px solid lightgray;
    }

    
    .fox {
        display: flex;
    }

    button {
       width: 80px;
       height: 50px;
        margin-top: 10px
    }

</style>