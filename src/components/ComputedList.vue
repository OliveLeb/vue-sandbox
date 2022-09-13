<script>
export default {
  name: 'ComputedList',
  props: {
    call: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      content: {
        truc: 'bonjour truc',
        actions: [
          {
            name: 'details',
            boutonClass: 'details-class',
            click: () => console.log('details'),
          },
          {
            name: 'transfer',
            boutonClass: 'transfer-class',
            click: () => console.log('transfer'),
          },
          {
            name: 'add',
            boutonClass: 'add-class',
            click: () => console.log('add'),
          },
          {
            name: 'talkgroup',
            boutonClass: 'talkgroup-class',
            click: () => console.log('talkgroup'),
          },
        ],
        trac: 'bonjour trac',
      },
      secondContent: {
        truc: 'This is the second content',
        actions: [],
      },
      CommonActions: [],
      actions: {
        tgActions: {
          name: 'talkgroup',
          boutonClass: 'talkgroup-class',
          click: () => console.log('talkgroup'),
          condition: {},
        },
        addAction: {
          name: 'add',
          boutonClass: 'add-class',
          click: () => console.log('add'),
          condition: {
            'call.type': 'FREECALL',
          },
        },
        transferAction: {
          name: 'transfer',
          boutonClass: 'transfer-class',
          click: () => console.log('transfer'),
          condition: {
            'call.type': 'EMERGENCY',
          },
        },
        detailsAction: {
          name: 'details',
          boutonClass: 'details-class',
          click: () => console.log('details'),
          condition: {},
        },
      },
    }
  },
  /*
  Details: all, > 1 participants
  Add: Freecall, Emergency
  Transfert: Freecall, 1 participants
*/
  computed: {
    list() {
      let filteredActions = this.content.actions
      // const arr = this.content.actions
      if (this.call.type === 'FREECALL' && this.call.participants.length <= 1) {
        filteredActions = filteredActions.filter(
          (el) => el.boutonClass !== 'details-class'
        )
      } else if (
        this.call.type !== 'FREECALL' ||
        this.call.type !== 'EMERGENCYFREECALL'
      ) {
        filteredActions = filteredActions.filter(
          (el) => el.boutonClass !== 'add-class'
        )
      } else {
        filteredActions = filteredActions.filter(
          (el) => el.boutonClass !== 'transfer-class'
        )
      }
      return {
        ...this.content,
        actions: filteredActions,
      }
    },
    secondList() {
      let actions = []
      for (let action of Object.values(this.actions)) {
        if (Object.keys(action.condition).length === 0) {
          actions.push(action)
        } else {
          let isValid = true
          for (let key in action.condition) {
            console.log(key)
            const property = this.isNestedProperties(key, this)
            console.log(property)
            if (property !== action.condition[key]) {
              isValid = false
              break
            }
          }
          if (isValid) {
            actions.push(action)
          }
        }
      }
      return {
        ...this.secondContent,
        actions,
      }
    },
  },
  methods: {
    isNestedProperties(path, obj, separator = '.') {
      return path.split(separator).reduce((acc, curr) => acc && acc[curr], obj)
    },
  },
}
</script>

<template>
  <div class="ctn">
    <button @click="action.click" v-for="action in secondList.actions">
      {{ action.name }}
    </button>
  </div>
  {{ this.call }}
</template>

<style>
.ctn {
  width: 500px;
  margin: auto;
  display: flex;
  gap: 10px;
}
</style>
