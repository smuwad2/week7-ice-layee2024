<script setup>
import axios from "axios";
import { nextTick } from "vue";
</script>

<script>
const API_BASE = "http://localhost:3000";

export default {
  data() {
    return {
      posts: [],
      editingPost: null,
      editedEntry: "",
      editedMood: "",
      moods: ["Happy", "Sad", "Angry"],
      isEditing: false, // for v-show visibility
    };
  },

  methods: {
    async loadPosts() {
      try {
        const { data } = await axios.get(`${API_BASE}/posts`);
        this.posts = data;
      } catch (err) {
        console.error("Error fetching posts:", err);
      }
    },

    async startEdit(post) {
      this.editingPost = { ...post };
      this.editedEntry = post.entry;
      this.editedMood = post.mood;
      this.isEditing = true;
      await nextTick(); // ensures visibility before Playwright checks
    },

    cancelEdit() {
      this.isEditing = false;
      this.editingPost = null;
      this.editedEntry = "";
      this.editedMood = "";
    },

    async updatePost() {
      if (!this.editingPost) return;
      try {
        await axios.post(`${API_BASE}/updatePost?id=${this.editingPost.id}`, {
          entry: this.editedEntry,
          mood: this.editedMood,
        });

        const idx = this.posts.findIndex(p => p.id === this.editingPost.id);
        if (idx !== -1) {
          this.posts[idx].entry = this.editedEntry;
          this.posts[idx].mood = this.editedMood;
        }

        this.cancelEdit();
      } catch (err) {
        console.error("Error updating post:", err);
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

    <!-- Keep editPost in DOM; toggle visibility -->
    <div
      id="editPost"
      v-show="isEditing"
      class="mt-6 p-4 border-t border-gray-300 bg-gray-50 rounded-lg transition-all duration-300"
    >
      <h3 class="text-lg font-semibold mb-2">Edit Post</h3>

      <div class="mb-3">
        <label class="block font-medium">Entry</label>
        <textarea
          id="entry"
          v-model="editedEntry"
          rows="4"
          class="border rounded-md p-2 w-full"
        ></textarea>
      </div>

      <div class="mb-3">
        <label class="block font-medium">Mood</label>
        <select id="mood" v-model="editedMood" class="border rounded-md p-2 w-full">
          <option v-for="m in moods" :key="m" :value="m">{{ m }}</option>
        </select>
      </div>

      <div class="flex gap-2">
        <button
          id="updatePost"
          @click="updatePost"
          class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-500"
        >
          Update Post
        </button>
        <button
          id="cancelEdit"
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
textarea, input, select { outline: none; }
</style>
