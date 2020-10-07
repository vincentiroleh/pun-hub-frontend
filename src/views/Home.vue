<template class="container">
  <div class="container mt-5 text-left">
    <form @submit="addMessage" class="mb-3" id="punForm">
      <div v-if="error" class="alert alert-dismissible alert-warning">
        <button type="button" class="close" data-dismiss="alert">
          &times;
        </button>
        <h4 class="alert-heading">Error!</h4>
        <p class="mb-0">{{ error }}</p>
      </div>
      <div class="form-group">
        <label for="username">Username</label>
        <input
          v-model="message.username"
          type="text"
          class="form-control"
          id="username"
          placeholder="Enter a screen name"
          required
        />
      </div>
      <div class="form-group">
        <label for="subject">Category</label>
        <input
          v-model="message.subject"
          type="text"
          class="form-control"
          id="subject"
          placeholder="Enter a subject for your wordplay"
          required
        />
      </div>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea
          v-model="message.message"
          class="form-control"
          id="message"
          rows="3"
          placeholder="Enter your wordplay here"
        ></textarea>
      </div>
      <div class="form-group">
        <label for="imageURL">Image URL</label>
        <input
          v-model="message.imageURL"
          type="url"
          class="form-control"
          id="imageURL"
          placeholder="URL to an image that describes your wordplay"
        />
      </div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <div
      class="list-unstyled"
      v-for="message in reversedMessages"
      :key="message._id"
    >
      <li class="media row container">
        <img
          v-if="message.imageURL"
          class="mr-3"
          :src="message.imageURL"
          :alt="message.subject"
        />
        <div class="media-body">
          <h4 class="mt-0 mb-1">{{ message.username }}</h4>
          <h5 class="mt-0 mb-1">{{ message.subject }}</h5>
          {{ message.message }}
          <br />
          <small>{{ message.created }}</small>
        </div>
      </li>
      <hr />
    </div>

    <div>
      <footer class="text-center mb-2">
        Made for fun by
        <a
          href="https://twitter.com/irolehvincent"
          target="_blank"
          rel="noopener noreferrer"
          class="bg-primary text-white"
        >Vincent Iroleh</a>
      </footer>
    </div>
  </div>
</template>

<script>
const API_URL = process.env.VUE_APP_MY_ENV_API_URL;

export default {
  name: 'home',
  data: () => ({
    error: '',
    messages: [],
    message: {
      username: '',
      subject: '',
      message: '',
      imageURL: '',
    },
  }),
  computed: {
    reversedMessages() {
      return this.messages.slice().reverse();
    },
  },
  mounted() {
    fetch(API_URL)
      .then((response) => response.json())
      .then((result) => {
        this.messages = result;
      });
  },
  methods: {
    addMessage() {
      // eslint-disable-next-line no-alert
      alert('Processing your pun!');
      fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(this.message),
        headers: {
          'Content-Type': 'application/json',
        },
      })
        .then((response) => response.json())
        .then((result) => {
          if (result.details) {
            const error = result.details
              .map((detail) => detail.message)
              .join('. ');
            this.error = error;
          } else {
            this.error = '';
            this.showMessageForm = false;
            this.message.push(result);
          }
        });
    },
  },
};
</script>

<style>
img {
  max-width: 300px;
  height: auto;
}
</style>
