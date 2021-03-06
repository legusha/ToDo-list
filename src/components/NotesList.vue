<template>
  <div class="d-flex justify-between flex-wrap notes-list p-4 border-secondary">
    <div
      v-for="(note, index) in list"
      :key="index"
      @mouseover="noteActive = index"
      @mouseleave="noteActive = null"
      class="card mb-3 note bg-white"
    >
      <div class="card-title note-title text-left d-flex align-center justify-between bg-secondary">
        <h3 class="m-0">{{note.name | replaceText(noteNameMaxSymbol)}}</h3>
        <div v-if="noteActive === index" class="note-title-action">
          <Icon
            v-for="icon in icons"
            :key="icon.symbol"
            :symbol="icon.symbol"
            :class-name="icon.className"
            @action="icon.handler(index)"
            class="mr-2"
          ></Icon>
        </div>
      </div>
      <div class="card-body note-body">
        <ToDoList
          :id="index.toString()"
          :list="note.todoList"
          :mutable="todoList.mutable"
          @action="toDoListAction"
        ></ToDoList>
        <p v-if="note.todoList.length === 0" class="m-0">Empty todo list</p>
      </div>
    </div>
    <Modal
      v-if="modalActive"
      :active="modalActive"
      @close="modalClose"
    >
      <template slot="header">
        <h2 class="m-0">Question:</h2>
      </template>
      <template slot="body">
        <h2>Are you sure you want to delete the note?</h2>
      </template>
      <template slot="footer">
        <div>
          <button @click="modalClose" type="button" class="btn-primary mr-4">Cancel</button>
          <button @click="modalAccept" type="button" class="btn-success">Accept</button>
        </div>
      </template>
    </Modal>
  </div>
</template>

<script>
export default {
  name: 'NotesList',
  props: {
    list: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      todoList: {
        mutable: false
      },
      iconClass: 'color',
      icons: {
        edit: {
          symbol: 'fa fa-pencil',
          handler: this.noteCurrentEdit,
          className: 'text-success'
        },
        remove: {
          symbol: 'fa fa-times',
          handler: this.modalOpen,
          className: 'text-danger'
        }
      },
      noteNameMaxSymbol: 12,
      noteActive: null,
      noteCurrentIndex: null,
      modalActive: false
    }
  },
  methods: {
    toDoListAction (list = []) {
      console.log(list)
    },
    modalClose () {
      this.noteCurrentIndex = null
      this.modalActive = false
    },
    modalOpen (index) {
      this.noteCurrentIndex = index
      this.modalActive = true
    },
    modalAccept () {
      this.$emit('noteRemove', this.noteCurrentIndex)
      this.modalClose()
    },
    noteCurrentEdit (index) {
      const params = { id: (++index).toString() }
      this.$router.push({ name: 'Note', params })
    }
  }
}
</script>

<style scoped lang="scss">
  .notes-list {
    .note {
      flex-basis: 270px;
      align-self: stretch;
      align-content: stretch;
      .note-title-action {
        font-size: 22px;
      }
    }
    .note:first-child{
      margin-left: 0;
    }
    @media(max-width: 768px) {
      .note {
        flex-basis: 100%;
      }
    }
  }
</style>
