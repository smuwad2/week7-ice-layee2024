<template>
  <div class="container mt-4">
    <h4>Blog Posts</h4>
    <table class="table table-striped">
      <thead>
        <tr>
          <th>ID</th>
          <th>Entry</th>
          <th>Mood</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td>
            <button class="btn btn-outline-dark btn-sm" @click="editPost(post)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit form -->
    <div id="editPost" v-if="showEdit" class="mt-4">
      <h5>Edit Post</h5>
      <div class="mb-3">
        <label for="entry">Entry:</label>
        <textarea id="entry" v-model="entry" class="form-control"></textarea>
      </div>

      <div class="mb-3">
        <label for="mood">Mood:</label>
        <select id="mood" v-model="mood" class="form-select">
          <option>Happy</option>
          <option>Sad</option>
          <option>Angry</option>
        </select>
      </div>

      <button class="btn btn-primary" @click="updatePost">Update Post</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      posts: [],
      showEdit: false,
      id: null,
      entry: "",
      mood: "",
    };
  },
  async mounted() {
    await this.getPosts();
  },
  methods: {
    async getPosts() {
      const res = await axios.get("http://localhost:3000/posts");
      this.posts = res.data;
    },
    editPost(post) {
      this.showEdit = true;
      this.id = post.id;
      this.entry = post.entry;
      this.mood = post.mood;
    },
    async updatePost() {
      await axios.post("http://localhost:3000/updatePost", {
        id: this.id,
        entry: this.entry,
        mood: this.mood,
      });
      this.showEdit = false;
      await this.getPosts();
    },
  },
};
</script>

<style scoped>
#editPost {
  border-top: 1px solid #ccc;
  padding-top: 1rem;
}
</style>

