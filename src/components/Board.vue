<template>
  <div
    :id="id"
    class="Board"
    @dragover.prevent
    @drop.prevent="drop"
  >
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'Board',
  props: ['id'],
  method: {
    drop: e => {
      const cardId = e.dataTransfer.getData('card_id')
      const card = document.getElementById(cardId)

      card.style.display = 'block'
      e.target.appendChild(card)
    }
  }
}
</script>
