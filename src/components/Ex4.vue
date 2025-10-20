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
            <button class="btn btn-secondary btn-sm" @click="editPost(post)">Edit</button>
            <button class="btn btn-danger btn-sm ms-2" @click="deletePost(post.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Add Post Form (Exercise 3) -->
    <div id="addPost" class="mt-4">
      <h5>Add Post</h5>
      <div class="mb-3">
        <label>Subject:</label>
        <input v-model="newPost.subject" class="form-control" />
      </div>
      <div class="mb-3">
        <label>Entry:</label>
        <textarea v-model="newPost.entry" class="form-control"></textarea>
      </div>
      <div class="mb-3">
        <label>Mood:</label>
        <select v-model="newPost.mood" class="form-select">
          <option>Happy</option>
          <option>Sad</option>
          <option>Angry</option>
        </select>
      </div>
      <button class="btn btn-primary" @click="addPost">Add Post</button>
    </div>

    <!-- Edit Post Form (Exercise 4) -->
    <div v-if="isEditing" id="editPost" class="mt-5">
      <h5>Edit Post</h5>
      <div class="mb-3">
        <label>Entry:</label>
        <textarea id="entry" v-model="editForm.entry" class="form-control"></textarea>
      </div>
      <div class="mb-3">
        <label>Mood:</label>
        <select id="mood" v-model="editForm.mood" class="form-select">
          <option>Happy</option>
          <option>Sad</option>
          <option>Angry</option>
        </select>
      </div>
      <button class="btn btn-primary" id="updatePost" @click="updatePost">Update Post</button>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {
      posts: [],
      newPost: {
        subject: "",
        entry: "",
        mood: "Happy",
      },
      isEditing: false,
      editForm: {
        id: null,
        entry: "",
        mood: "",
      },
    }
  },
  created() {
    this.loadPosts()
  },
  methods: {
    async loadPosts() {
      const res = await axios.get("http://localhost:3000/posts")
      this.posts = res.data
    },
    async addPost() {
      await axios.post("http://localhost:3000/addPost", this.newPost)
      this.newPost = { subject: "", entry: "", mood: "Happy" }
      await this.loadPosts()
    },
    async deletePost(id) {
      await axios.post("http://localhost:3000/deletePost", { id })
      await this.loadPosts()
    },
    editPost(post) {
      this.isEditing = true
      this.editForm.id = post.id
      this.editForm.entry = post.entry
      this.editForm.mood = post.mood
    },
    async updatePost() {
      await axios.post("http://localhost:3000/updatePost", this.editForm)
      this.isEditing = false
      await this.loadPosts()
    },
  },
}
</script>
