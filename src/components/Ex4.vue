<template>
  <section class="p-8">
    <h2 class="text-2xl font-bold mb-4">Blog Posts</h2>

    <table class="w-full border-collapse border border-gray-300">
      <thead>
        <tr class="bg-gray-100">
          <th class="border border-gray-300 p-2">ID</th>
          <th class="border border-gray-300 p-2 text-left">Entry</th>
          <th class="border border-gray-300 p-2">Mood</th>
          <th class="border border-gray-300 p-2">Actions</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td class="border border-gray-300 p-2 text-center">{{ post.id }}</td>
          <td class="border border-gray-300 p-2">{{ post.entry }}</td>
          <td class="border border-gray-300 p-2 text-center">{{ post.mood }}</td>
          <td class="border border-gray-300 p-2 text-center">
            <button
              class="bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600"
              @click="editPost(post)"
            >
              Edit
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit Form -->
    <div v-if="editingPost" class="mt-8 p-6 border rounded-lg bg-gray-50">
      <h3 class="text-xl font-semibold mb-4">Edit Post</h3>

      <div class="mb-3">
        <label class="block mb-1 font-medium">Entry</label>
        <textarea
          v-model="editingPost.entry"
          class="w-full border border-gray-300 rounded p-2"
          rows="3"
        ></textarea>
      </div>

      <div class="mb-3">
        <label class="block mb-1 font-medium">Mood</label>
        <select
          v-model="editingPost.mood"
          class="border border-gray-300 rounded p-2 w-full"
        >
          <option>Happy</option>
          <option>Sad</option>
          <option>Angry</option>
          <option>Neutral</option>
        </select>
      </div>

      <div class="flex gap-3 mt-4">
        <button
          class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700"
          @click="updatePost"
        >
          Update Post
        </button>

        <button
          class="bg-gray-400 text-white px-4 py-2 rounded hover:bg-gray-500"
          @click="cancelEdit"
        >
          Cancel
        </button>
      </div>
    </div>
  </section>
</template>

<script>
import axios from "axios"

export default {
  name: "ViewPosts",
  data() {
    return {
      posts: [
        { id: 6, entry: "I am returning home now.", mood: "Happy" },
        { id: 5, entry: "A very handsome boy gave me roses for Vday and asked me out!", mood: "Happy" },
        { id: 4, entry: "This crazy woman gave us CNY homework omg can she pliz go enjoy CNY and leave us alone?", mood: "Angry" },
        { id: 3, entry: "Mummy says she will give away my dog because he pees everywhere", mood: "Angry" },
        { id: 2, entry: "I got F on my quiz I am terrible", mood: "Sad" },
        { id: 1, entry: "Another term started omg i wanna die alrdy…", mood: "Sad" },
      ],
      editingPost: null,
    }
  },
  methods: {
    editPost(post) {
      // clone so editing doesn't immediately affect list
      this.editingPost = { ...post }
    },
    cancelEdit() {
      this.editingPost = null
    },
    async updatePost() {
      try {
        // Send updated data to backend
        await axios.post("/updatePost", this.editingPost)

        // Update the local posts array
        const idx = this.posts.findIndex(p => p.id === this.editingPost.id)
        if (idx !== -1) this.posts[idx] = { ...this.editingPost }

        // Hide edit form
        this.editingPost = null
        alert("Post updated successfully!")
      } catch (err) {
        console.error("Update failed:", err)
        alert("Failed to update post.")
      }
    },
  },
}
</script>

<style scoped>
table {
  font-family: Arial, sans-serif;
  font-size: 15px;
}
</style>


