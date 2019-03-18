<template>
  <div>
    <div class="titlebar">
      <p>トークルーム</p>
    </div>
    <div class="chatspace">
      <ul>
        <li
          v-for="item in $data.messageList"
          :key="item.id"
        >
          <span class="list__name">{{ item.name }}</span>
          <span class="list__item">{{ item.text }}</span>
          <span class="list__time">{{ item.time }}</span>
        </li>
      </ul>
    </div>
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
        text: 'アイウエオ',
        time: '18:09'
      }
    ];

    return {
      // map: 配列内のすべての要素に処理を行い、その戻り値から新しい配列を作成する
      messageList: messageList.map((item, index) => ({ ...item, id: index })),
      nextMessageId: messageList.length,
      name: '',
      text: '',
      time: ''
    };
  },
  created() {
    // 受け取る
    socket.on('connect', () => {
      console.log('connected!');
      // this.$data.text = 'connected';
      // socket.emit('connect', this.onSubmit());
    });
    socket.on('send', (item) => {
      console.log(item);
      this.$data.message = item;
    });
  },
  methods: {
    /**
     * 入室したとき
     */
    /**
     * Enterボタンを押したとき
     */
    onSubmit(e) {
      const date = new Date();
      const hour = date.getHours();
      let minutes = date.getMinutes();
      if (minutes < 10) {
        minutes = `0${minutes}`;
      }
      const label = `${hour}:${minutes}`;
      // フォーム遷移を防ぐ
      e.preventDefault();
      socket.emit('send', { name: this.$data.name, text: this.$data.text, time: label });
      this.$data.messageList.push({
        id: this.$data.nextMessageId,
        name: this.$data.name,
        text: this.$data.text,
        time: label
      });
      this.$data.nextMessageId += 1;
    },
  }
};

</script>

<style lang="scss" scoped>
.titlebar {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  height: 50px;
  background-color: #333;
  width: 100%;

  p {
    color: white;
    width: 100px;
    margin-left: auto;
    margin-right: auto;
  }
}

.chatspace {
  position: absolute;
  top: 60px;
  right: 0;
  left: 0;
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
    border-radius: 10px;
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

  .list__time {
    position: relative;
    top: 7px;
    font-size: 8px;
    color: gray;
  }
}

form {
  position: fixed;
  bottom: 0;
  padding: 10px;
}

</style>
