<script setup>
import axios from "axios";
</script>

<script>
export default {
  data() {
    return {
      posts: [],
      editingPost: null, // holds the post being edited
      editedEntry: "",
      editedMood: "",
      moods: ["Happy", "Sad", "Angry"],
    };
  },

  computed: {
    baseUrl() {
      // Works in both localhost and Codespaces
      if (window.location.hostname === "localhost")
        return "http://localhost:3000";
      const codespace_host = window.location.hostname.replace("5173", "3000");
      return `https://${codespace_host}`;
    },
  },

  methods: {
    async loadPosts() {
      try {
        const response = await axios.get(`${this.baseUrl}/posts`);
        this.posts = response.data;
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    },

    startEdit(post) {
      this.editingPost = { ...post }; // copy to avoid direct mutation
      this.editedEntry = post.entry;
      this.editedMood = post.mood;
    },

    cancelEdit() {
      this.editingPost = null;
      this.editedEntry = "";
      this.editedMood = "";
    },

    async updatePost() {
      try {
        await axios.post(
          `${this.baseUrl}/updatePost?id=${this.editingPost.id}`,
          {
            entry: this.editedEntry,
            mood: this.editedMood,
          }
        );

        // Reflect changes in UI immediately
        const index = this.posts.findIndex(
          (p) => p.id === this.editingPost.id
        );
        if (index !== -1) {
          this.posts[index].entry = this.editedEntry;
          this.posts[index].mood = this.editedMood;
        }

        // Hide edit form
        this.cancelEdit();
      } catch (error) {
        console.error("Error updating post:", error);
      }
    },
  },

  mounted() {
    this.loadPosts();
  },
};
</script>

<template>
  <div class="p-6 max-w-4xl mx-auto">
    <h2 class="text-2xl font-bold mb-4">Blog Posts</h2>

    <table class="min-w-full border border-gray-300 rounded-lg text-sm">
      <thead class="bg-gray-100">
        <tr>
          <th class="p-2 border">ID</th>
          <th class="p-2 border">Entry</th>
          <th class="p-2 border">Mood</th>
          <th class="p-2 border">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id" class="hover:bg-gray-50">
          <td class="p-2 border">{{ post.id }}</td>
          <td class="p-2 border">{{ post.entry }}</td>
          <td class="p-2 border">{{ post.mood }}</td>
          <td class="p-2 border text-center">
            <button
              @click="startEdit(post)"
              class="px-3 py-1 bg-blue-500 text-white rounded hover:bg-blue-400"
            >
              Edit
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit form (only shown when editing) -->
    <div
      v-if="editingPost"
      class="mt-6 p-4 border-t border-gray-300 bg-gray-50 rounded-lg"
    >
      <h3 class="text-lg font-semibold mb-2">Edit Post</h3>

      <div class="mb-3">
        <label class="block font-medium">Entry</label>
        <textarea
          v-model="editedEntry"
          rows="4"
          class="border rounded-md p-2 w-full"
        ></textarea>
      </div>

      <div class="mb-3">
        <label class="block font-medium">Mood</label>
        <select v-model="editedMood" class="border rounded-md p-2 w-full">
          <option v-for="m in moods" :key="m" :value="m">
            {{ m }}
          </option>
        </select>
      </div>

      <div class="flex gap-2">
        <button
          @click="updatePost"
          class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-500"
        >
          Update Post
        </button>
        <button
          @click="cancelEdit"
          class="bg-gray-400 text-white px-4 py-2 rounded hover:bg-gray-300"
        >
          Cancel
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
textarea,
input,
select {
  outline: none;
}
</style>
