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
