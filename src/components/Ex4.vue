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
            <button
              class="btn btn-secondary btn-sm"
              @click="openEdit(post)"
            >
              Edit
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit Form -->
    <div :style="{ display: isEditing ? 'block' : 'none' }" id="editPost" class="mt-4">
      <h5>Edit Post</h5>
      <div class="mb-3">
        <label for="entry">Entry:</label>
        <textarea
          id="entry"
          v-model="editForm.entry"
          class="form-control"
        ></textarea>
      </div>

      <div class="mb-3">
        <label for="mood">Mood:</label>
        <select id="mood" v-model="editForm.mood" class="form-select">
          <option>Happy</option>
          <option>Sad</option>
          <option>Angry</option>
        </select>
      </div>

      <button id="updatePost" class="btn btn-primary" @click="updatePost">
        Update Post
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {
      posts: [],
      isEditing: false,
      editForm: {
        id: null,
        entry: "",
        mood: "",
      },
    }
  },
  async mounted() {
    await this.loadPosts()
  },
  methods: {
    async loadPosts() {
      const res = await axios.get("http://localhost:3000/posts")
      this.posts = res.data
    },
    openEdit(post) {
      // Immediately visible to Playwright
      this.isEditing = true
      this.editForm = { ...post }
    },
    async updatePost() {
      const payload = {
        id: this.editForm.id,
        entry: this.editForm.entry,
        mood: this.editForm.mood,
      }

      await axios.post(`http://localhost:3000/updatePost?id=${this.editForm.id}`, payload)
      this.isEditing = false
      await this.loadPosts()
    },
  },
}
</script>
