<template v-for="item in $props.messageList">
        <li
          :key="item.id"
          class="item"
          @click="$emit('check', item.id)"
        >
          <span class="item__text">{{ item.text }}</span>
        </li>
      </template>

<script>
import VueTypes from 'vue-types';

export default {
  props: {
    messageList: VueTypes.arrayOf(VueTypes.shape({
      id: VueTypes.number.isRequired,
      text: VueTypes.string.isRequired
    })).isRequired
  }
};
</script>

<style lang="scss" scoped>
//
</style>
