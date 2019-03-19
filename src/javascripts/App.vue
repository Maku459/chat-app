<template>
  <div>
    <div class="titlebar">
      <p>トークルーム</p>
    </div>
    <div class="middle">
      <div class="chatspace">
        <ul>
          <transition-group name="fade">
            <li
              v-for="item in $data.messageList"
              :key="item.id"
              v-if="$data.show"
            >
              <span class="list__name">{{ item.name }}</span>
              <span class="list__item">{{ item.text }}</span>
              <span class="list__time">{{ item.time }}</span>
            </li>
          </transition-group>
        </ul>
      </div>
    </div>
    <div class="footer">
      <form @submit="onSubmit">
        <div class="form">
          名前：
          <input v-model="$data.name" type="text">
        </div>
        <div class="form">
          テキスト：
          <input v-model="$data.text" type="text">
          <button type="submit">送信</button>
        </div>
      </form>
    </div>
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
        time: '18:09',
        show: true
      }
    ];

    return {
      // map: 配列内のすべての要素に処理を行い、その戻り値から新しい配列を作成する
      messageList: messageList.map((item, index) => ({ ...item, id: index })),
      nextMessageId: messageList.length,
      name: '',
      text: '',
      time: '',
      show: true
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
      // this.$data.item = item;
      socket.emit('send', item);
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
        time: label,
        show: true
      });
      this.$data.nextMessageId += 1;
    },
  }
};

</script>

<style lang="scss" scoped>
@import url('https://fonts.googleapis.com/css?family=Noto+Sans+JP');

* {
  font-family: 'Noto Sans JP', sans-serif;
}

.middle {
  position: absolute;
  top: 50px;
  right: 0;
  left: 0;
  height: 100%;
  background-color: aliceblue;
  overflow: scroll;
  z-index: 10;

  .chatspace {
    position: absolute;
    right: 0;
    left: 0;
  }
}

.titlebar {
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  height: 50px;
  background-color: #333;
  width: 100%;
  z-index: 30;

  p {
    color: white;
    width: 100px;
    margin-left: auto;
    margin-right: auto;
  }
}

.footer {
  position: fixed;
  bottom: 0;
  right: 0;
  left: 0;
  // height: 40px;
  width: 100%;
  border: solid 1px #999;
  background-color: white;
  padding: 10px;
  z-index: 20;

  .form {
    display: inline-block;
    padding: 5px;
  }
}

ul {
  padding-left: 20px;
}

li {
  position: relative;
  list-style: none;
  padding-top: 17px;
  padding-bottom: 17px;

  .list__name {
    position: relative;
    padding-right: 10px;
  }

  .list__item {
    position: relative;
    box-sizing: border-box;
    padding: 7px 10px;
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

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
