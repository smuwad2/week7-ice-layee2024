<script setup>
import axios from "axios";
</script>

<script>
const API_BASE = "http://localhost:3000"; // stable for tests

export default {
  data() {
    return {
      moods: ["Happy", "Sad", "Angry"],
      selectedMood: "Happy",
      subject: "",
      entry: "",
      statusMsg: "",
    };
  },

  methods: {
    async submit() {
      try {
        // spec for Ex3 uses GET /addPost with query params
        await axios.get(`${API_BASE}/addPost`, {
          params: {
            subject: this.subject,
            entry: this.entry,
            mood: this.selectedMood,
          },
        });

        this.statusMsg = "Post added successfully!";
        // clear inputs (ok for CT)
        this.subject = "";
        this.entry = "";
        this.selectedMood = "Happy";
      } catch (error) {
        this.statusMsg = "Error adding post: " + error.message;
        // still surface in console for visibility
        console.error(error);
      }
    },
  },
};
</script>

<template>
  <div class="m-4 p-4 border rounded-lg max-w-2xl mx-auto bg-white shadow-md">
    <h3 class="text-xl font-bold mb-3 text-gray-800">Add a New Blog Post</h3>

    <div class="space-y-3">
      <div>
        <label class="font-semibold">Subject:</label>
        <input
          id="subject"
          type="text"
          v-model="subject"
          class="border rounded-md p-2 w-full"
          required
        />
      </div>

      <div>
        <label class="font-semibold">Entry:</label>
        <textarea
          id="entry"
          v-model="entry"
          rows="5"
          class="border rounded-md p-2 w-full"
          required
        ></textarea>
      </div>

      <div>
        <label class="font-semibold">Mood:</label>
        <select id="mood" v-model="selectedMood" class="border rounded-md p-2 w-full">
          <option v-for="m in moods" :key="m" :value="m">{{ m }}</option>
        </select>
      </div>

      <button
        id="submitPost"
        @click="submit"
        class="bg-blue-600 text-white py-2 px-4 rounded hover:bg-blue-500"
      >
        Submit New Post
      </button>

      <p class="text-sm text-gray-700 mt-2">{{ statusMsg }}</p>

      <hr class="my-4" />
      <p class="text-gray-600">
        Click <router-link to="/ViewPosts/" class="text-blue-600 underline">here</router-link>
        to return to Main Page
      </p>
    </div>
  </div>
</template>

<style scoped>
textarea, input, select { outline: none; }
</style>