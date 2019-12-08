<template>
    <div
      :id="id"
      class="Card"
      :draggable="draggable"
      @dragstart="dragStart"
      @dragover.stop
    >
      <slot></slot>
    </div>
</template>

<script>
export default {
  name: 'Card',
  props: ['id', 'draggable'],
  methods: {
    dragStart: e => {
      const target = e.target
      e.dataTransfer.setData('card_id', target.id)
      setTimeout(() => {
        target.style.display = 'none'
      })
    }
  }
}
</script>
