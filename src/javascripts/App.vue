<template>
  <div class="overall">
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
              <div class="div__item"  v-bind:class="{message__green: item.class === 'message__green', message__white: item.class === 'message__white', connection: item.class === 'connection'}">
                <span class="list__item">{{ item.text }}</span>
              </div>
              <span class="list__time">{{ item.time }}</span>
            </li>
          </transition-group>
        </ul>
      </div>
    </div>
    <div class="footer">
      <transition>
        <form @submit="onSubmit" v-show="visible">
          <div class="form">
            名前：
            <input v-model="$data.name" type="text">
          </div>
          <div class="form">
            テキスト：
            <input v-model="$data.text" type="text" class="form__text">
            <button class="button__submit" type="submit">送信</button>
          </div>
        </form>
      </transition>
      <div class="button__visible" v-on:click="toggle">
        <img src="../images/arrow.svg" width="16px" alt="close" v-bind:class="{ rotate: visible }">
      </div>
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
    const messageList = [];
    return {
      // map: 配列内のすべての要素に処理を行い、その戻り値から新しい配列を作成する
      messageList: messageList.map((item, index) => ({ ...item, id: index })),
      nextMessageId: messageList.length,
      name: '',
      text: '',
      time: '',
      class: '',
      show: true,
      visible: true,
      number: null
    };
  },
  created() {
    // 受け取る
    socket.on('connect', (item) => {
      this.$data.messageList.push({
        id: this.$data.nextMessageId,
        text: 'connected!',
        class: 'connection',
        show: true
      });
      this.$data.nextMessageId += 1;
      this.$data.number += 1;
      console.log(this.$data.number);
    });
    socket.on('disconnect', (item) => {
      this.$data.messageList.push({
        id: this.$data.nextMessageId,
        text: 'disconnected!',
        class: 'connection',
        show: true
      });
      this.$data.nextMessageId += 1;
      this.$data.number -= 1;
      console.log(this.$data.number);
    });
    socket.on('send', (item) => {
      this.$data.messageList.push({
        id: this.$data.nextMessageId,
        name: item.name,
        text: item.text,
        time: item.time,
        class: 'message__white',
        show: true
      });
      this.$data.nextMessageId += 1;
    });
  },
  methods: {
    /**
     * フォーム開閉
     */
    toggle() {
      this.visible = !this.visible;
    },
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
      if (this.$data.name !== '' && this.$data.text !== '') {
        socket.emit('send', { name: this.$data.name, text: this.$data.text, time: label });
        this.$data.messageList.push({
          id: this.$data.nextMessageId,
          name: this.$data.name,
          text: this.$data.text,
          time: label,
          class: 'message__green',
          show: true
        });
        this.$data.nextMessageId += 1;
        this.$data.text = '';
      }
    },
  }
};
</script>

<style lang="scss" scoped>
@import url('https://fonts.googleapis.com/css?family=Noto+Sans+JP');

* {
  font-family: 'Noto Sans JP', sans-serif;
}

.overall {
  display: flex;
  flex-direction: column;
}

.middle {
  position: absolute;
  top: 50px;
  right: 0;
  left: 0;
  height: 100%;
  background-color: lightblue;
  overflow: scroll;
  z-index: 10;

  .chatspace {
    position: absolute;
    right: 0;
    left: 0;
    margin-bottom: 120px;
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
    color: #fff;
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
  background-color: rgba(255, 255, 255, 0.7);
  padding: 10px;
  padding-top: 20px;
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
  padding-top: 7px;
  padding-bottom: 7px;
  max-width: 95vw;
  line-height: 2;
  display: flex;

  .list__name {
    position: relative;
    padding-right: 10px;
  }

  .list__item {
    position: relative;
    box-sizing: border-box;
  }

  .list__time {
    position: relative;
    top: 7px;
    font-size: 8px;
    color: gray;
    margin-left: 5px;
    margin-top: auto;
    margin-bottom: 3px;
  }
}

.div__item {
  position: relative;
  padding: 4px 10px;
  border-radius: 10px;
  display: inline-block;
  max-width: calc(95% - 70px);

  &.message__green {
    background-color: yellowgreen;

    &::before {
      position: absolute;
      content: '';
      top: 18px;
      left: -14px;
      margin-top: -7px;
      border: 7px solid transparent;
      border-right: 7px solid yellowgreen;
    }
  }

  &.message__white {
    background-color: #fff;

    &::before {
      position: absolute;
      content: '';
      top: 18px;
      left: -14px;
      margin-top: -7px;
      border: 7px solid transparent;
      border-right: 7px solid #fff;
    }
  }

  &.connection {
    background-color: #999;
    color: #fff;
  }
}

.button__submit {
  display: inline-block;
  padding: 0.3em 1em;
  text-decoration: none;
  color: #333;
  background-color: #fff;
  border: solid 1px #333;
  border-radius: 5px;
  transition: 0.4s;
  margin-left: 8px;
  margin-top: 5px;

  &:hover {
    background: #333;
    color: #fff;
  }
}

.button__visible {
  position: absolute;
  top: 10px;
  right: 30px;
}

.rotate {
  transform: rotate(-180deg);
  transition-duration: 0.3s;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
