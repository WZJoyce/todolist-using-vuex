<template>
  <div id="app">
    <a-input placeholder="Please input event" class="my_ipt" :value="inputValue" @change="handleInputChange" />
    <a-button type="primary" @click="addItemToList">Add Event</a-button>

    <a-list bordered :dataSource="infolist" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <a-checkbox :checked="item.done" @change="(e) => {cbStatusChanged(e, item.id)}">{{item.info}}</a-checkbox>
        <a slot="actions" @click="removeItemById(item.id)">Delete</a>
      </a-list-item>

      <div slot="footer" class="footer">
        <span>{{unDoneLength}} Remaining</span>
        <a-button-group>
          <a-button :type="viewKey === 'all' ? 'primary' : 'default'" @click="changeList('all')">All</a-button>
          <a-button :type="viewKey === 'undone' ? 'primary' : 'default'" @click="changeList('undone')">To do</a-button>
          <a-button :type="viewKey === 'done' ? 'primary' : 'default'" @click="changeList('done')">Done</a-button>
        </a-button-group>
        <a @click="clean">Delete all done events</a>
      </div>
    </a-list>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'

export default {
  name: 'app',
  data () {
    return {}
  },
  created () {
    this.$store.dispatch('getList')
  },
  computed: {
    ...mapState(['inputValue', 'viewKey']),
    ...mapGetters(['unDoneLength', 'infolist'])
  },
  methods: {
    handleInputChange (e) {
      this.$store.commit('setInputValue', e.target.value)
    },

    addItemToList () {
      if (this.inputValue.trim().length <= 0) {
        return this.$message.warning('Input is empty！')
      }

      this.$store.commit('addItem')
    },

    removeItemById (id) {
      this.$store.commit('removeItem', id)
    },

    cbStatusChanged (e, id) {
      const param = {
        id: id,
        status: e.target.checked
      }

      this.$store.commit('changeStatus', param)
    },
    clean () {
      this.$store.commit('cleanDone')
    },
    changeList (key) {
      this.$store.commit('changeViewKey', key)
    }
  }
}
</script>

<style scoped>
#app {
  padding: 10px;
  position: absolute;
  left: 25%;
  right:25%;
  width: 50%;
}

.my_ipt {
  width: 500px;
  margin-right: 10px;
}

.dt_list {
  width: 500px;
  margin-top: 10px;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
