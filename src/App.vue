<template>
  <div>
    <form @submit.prevent="save">
      <input type="text" v-model="form.title" placeholder="Title" /><br>
      <textarea v-model="form.content" placeholder="Content"></textarea><br>
      <button type="submit">Save</button>
    </form>
    <ul>
      <li v-for="article in articles" :key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)">Edit</button>
        <button @click="remove(article.id)">Delete</button>
      </li>
    </ul>
    <button @click="load">Load</button>
    <p style="padding:10px" class="text-h6">Welcome to Data Buku View</p>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: ''
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id ? `http://localhost:3000/articles/${form.id}` : 'http://localhost:3000/articles';
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, form);

        if (method === 'put') {
          const index = articles.value.findIndex(article => article.id === response.data.id);
          if (index !== -1) {
            articles.value[index] = response.data;
          }
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  }
}
</script>

<style scoped>
/* General Styling */
body {
  font-family: 'Arial, sans-serif';
  background-color: #f7f8fc;
  color: #333;
  margin: 0;
  padding: 20px;
}

h3 {
  margin: 0;
  color: #333;
}

/* Form Styling */
form {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
}

input[type="text"],
textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

button[type="submit"] {
  background-color: #a5d6a7;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

button[type="submit"]:hover {
  background-color: #81c784;
}

/* Article List Styling */
ul {
  list-style-type: none;
  padding: 0;
}

li {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
}

li h3 {
  margin-bottom: 10px;
}

li p {
  margin-bottom: 10px;
}

/* Button Styling */
button {
  background-color: #ffcc80;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-right: 10px;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #ffb74d;
}

button:last-child {
  background-color: #ef9a9a;
}

button:last-child:hover {
  background-color: #e57373;
}

/* Welcome Text Styling */
.text-h6 {
  font-size: 20px;
  color: #333;
  margin-top: 20px;
  text-align: center;
}
</style>
