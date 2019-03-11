<template>
  <div>
    <p>
      <img class="logo" src="../images/logo.jpg" alt="ロゴ">
      <span class="sample">テスト</span>
    </p>
    <MyComponent :messageList="$data.messageList" />
    <form @submit="onSubmit">
      <input v-model="$data.text" type="text">
      <button type="submit">送信</button>
    </form>
  </div>
</template>

<script>
import socket from './utils/socket';
// components
import MyComponent from './components/MyComponent.vue';
export default {
  components: {
    MyComponent
  },
  data() {
    const messageList = [
      {
        text: 'アイウエオ'
      }
    ];
    return {
      messageList: messageList.map((item, index) => ({ ...item, id: index })),
      nextMessageId: messageList.length,
      message: '',
      text: ''
    };
  },
  created() {
    socket.on('connect', () => {
      console.log('connected!');
    });
    socket.on('send', (message) => {
      console.log(message);
      this.$data.message = message;
    });
  },
  methods: {
    /**
     * Enterボタンを押したとき
     */
    // onSubmit(e) {
    //   e.preventDefault();
    //   socket.emit('send', this.$data.text);
    // },

    onSubmit(e) {
      e.preventDefault();
      socket.emit('send', this.$data.text);
      this.$data.messageList.unshift({
        id: this.$data.nextMessageId,
        text: this.$data.text
      });
      this.$data.nextMessageId += 1;
    },
  }
};
</script>

<style lang="scss" scoped>
.logo {
  width: 40px;
}

.sample {
  color: $red;
}
</style>
