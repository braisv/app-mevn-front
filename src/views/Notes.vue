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
              @click="activateEdition(item._id)"
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
      message: { color: "danger", text: "" },
      dismissSecs: 5,
      dismissCountDown: 0,
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
    alertText(text) {
      this.message.text = text;
      this.showAlert();
    },
  },
};
</script>
