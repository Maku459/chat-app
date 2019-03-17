<template>
  <div>
    <p>
      <span class="sample">チャット</span>
    </p>
    <ul>
        <li
          v-for="item in $data.messageList"
          :key="item.id"
        >
          <span class="list__name">{{ item.name }}</span>
          <span class="list__item">{{ item.text }}</span>
        </li>
      </ul>
    <form @submit="onSubmit">
      名前：
      <input v-model="$data.name" type="text">
      テキスト：
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
        name: 'a',
        text: 'アイウエオ'
      }
    ];
    return {
      // map: 配列内のすべての要素に処理を行い、その戻り値から新しい配列を作成する
      messageList: messageList.map((item, index) => ({ ...item, id: index })),
      nextMessageId: messageList.length,
      name: '',
      text: ''
    };
  },
  created() {
    // 受け取る
    socket.on('connect', () => {
      console.log('connected!');
      // this.$data.text = 'connected';
      // socket.emit('connect', this.onSubmit());
    });
    // socket.on('send', (message) => {
    //   console.log(message);
    //   this.$data.message = message;
    // });
  },
  methods: {
    /**
     * Enterボタンを押したとき
     */
    onSubmit(e) {
      // フォーム遷移を防ぐ
      e.preventDefault();
      socket.emit('send', { name: this.$data.name, text: this.$data.text });
      this.$data.messageList.push({
        id: this.$data.nextMessageId,
        name: this.$data.name,
        text: this.$data.text
      });
      this.$data.nextMessageId += 1;
    },
  }
};
</script>

<style lang="scss" scoped>
.sample {
  color: $red;
}

li {
  position: relative;
  list-style: none;
  padding-top: 15px;
  padding-bottom: 15px;

  .list__name {
    position: relative;
    padding-right: 10px;
  }

  .list__item {
    position: relative;
    box-sizing: border-box;
    padding: 10px;
    border-radius: 5px;
    background-color: yellowgreen;

    &::before {
      position: absolute;
      content: '';
      top: 50%;
      left: -14px;
      margin-top: -7px;
      border: 7px solid transparent;
      border-right: 7px solid yellowgreen;
    }
  }
}

form {
  position: fixed;
  bottom: 0;
  padding: 10px;
}

</style>
