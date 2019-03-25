<template>
  <div class="card card-body mb-3">
    {{ card.name }}
  </div>



  <div class="modal" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Modal title</h5>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <p>Modal body text goes here.</p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary">Save changes</button>
          <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>


</template>

<script>
export default {
  // components: { card },
  props: ["card"],
  data() {
    return {
      editing: false
    }
  },

  methods: {
    startEditing() {
      this.editing = true;
      this.$nextTick(() => { this.$refs.message.focus() });
    },
    cardMoved(event) {
      const evt = event.added || event.moved;
      if (evt == undefined) { return };

      const element = evt.element;

      const list_index = window.store.lists.findIndex((list) => {
        return list.cards.find((card) => {
          return card.id === element.id;
        })
      })

      let data = new FormData;
      data.append("card[list_id]", window.store.lists[list_index].id);
      data.append("card[position]", evt.newIndex + 1);

      Rails.ajax({
        url: `cards/${element.id}/move`,
        type: "PATCH",
        data,
        dataType: "json"
      })
    },
    submitMessage() {
      let data = new FormData;
      data.append("card[list_id]", this.list.id);
      data.append("card[name]", this.message);

      Rails.ajax({
        url: "/cards",
        type: "POST",
        data,
        dataType: "json",
        success:(data) => {
          const index = window.store.lists.findIndex(item => item.id == this.list.id);
          window.store.lists[index].cards.push(data);
          this.message = "";
          this.$nextTick(() => { this.$refs.message.focus() });
        }
      })
    }
  }
}
</script>


<style scoped>
/*.card {
  background: #E2E4E6;
  border-radius: 3px;
  padding: 10px;
  display: inline-block;
  vertical-align: top;
  margin-right: 20px;
  width: 270px;
}*/
</style>
