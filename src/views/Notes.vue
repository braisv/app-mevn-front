<template>
  <div class="container">
    <h1>Notes</h1>
    <b-alert
      :show="dismissCountDown"
      dismissible
      :variant="message.color"
      @dismissed="dismissCountDown = 0"
      @dismiss-count-down="countDownChanged"
    >
      {{ message.text }}
    </b-alert>
    <form @submit.prevent="addNote()">
      <h3>Add new note</h3>
      <input
        type="text"
        class="form-control my-2"
        placeholder="Name"
        v-model="note.name"
      />
      <input
        type="text"
        class="form-control my-2"
        placeholder="Description"
        v-model="note.description"
      />
      <b-button class="btn-success my-2 btn-block" type="submit"
        >Add Note</b-button
      >
    </form>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Name</th>
          <th scope="col">Description</th>
          <th scope="col">Date</th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in notes" :key="index">
          <th scope="row">{{ item._id }}</th>
          <td>{{ item.name }}</td>
          <td>{{ item.description }}</td>
          <td>{{ item.date }}</td>
          <td>
            <b-button
              class="btn-warning btn-sm mx-2"
              @click="updateNote(item._id)"
              >Actualizar</b-button
            >
            <b-button
              class="btn-danger btn-sm mx-2"
              @click="removeNote(item._id)"
              >Eliminar</b-button
            >
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      notes: [],
      message: { color: "", text: "" },
      dismissSecs: 5,
      dismissCountDown: 0,
      note: { name: "", description: "" },
    };
  },
  created() {
    this.getNotes();
  },
  methods: {
    getNotes() {
      this.axios
        .get("/allnotes")
        .then((res) => {
          this.notes = res.data;
        })
        .catch((err) => {
          console.log(err.response);
        });
    },
    countDownChanged(dismissCountDown) {
      this.dismissCountDown = dismissCountDown;
    },
    showAlert() {
      this.dismissCountDown = this.dismissSecs;
    },
    alertText(text, color) {
      this.message.text = text;
      this.message.color = color;
      this.showAlert();
    },
    addNote() {
      this.axios
        .post("/newnote", this.note)
        .then((res) => {
          this.notes.push(res.data);
          this.alertText("Created new note!", "success");
        })
        .catch((err) => {
          console.log(err.response.data);
          this.alertText(err.response.data.message, "danger");
        });
    },
    removeNote(id) {
      this.axios
        .delete(`/note/${id}`)
        .then((res) => {
          const index = this.notes.findIndex((el) => el._id === res.data._id);
          this.notes.splice(index, 1);
          this.alertText("Deleted note!", "success");
        })
        .catch((err) => {
          console.log(err.response.data);
          this.alertText(err.response.data.message, "danger");
        });
    },
    updateNote(id) {
      this.axios
        .put("/note")
        .then((res) => {})
        .catch((err) => {
          console.log(err.response.data);
          this.alertText(err.response.data.message, "danger");
        });
    },
  },
};
</script>
