<template>
  <div class="container">
    <h1 class="page-title">Search Box</h1>
    <input
      type="text"
      class="search-input"
      placeholder="Search..."
      v-model="searchTerm"
    />

    <div class="article-list">
      <div
        v-for="(article, index) in filteredArticles"
        :key="index"
        class="article"
      >
        <h2
          class="article-title"
          v-html="highlightKeywords(article.title)"
        ></h2>
        <p
          class="article-description"
          v-html="highlightKeywords(article.description)"
        ></p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      searchTerm: "",
      articles: [], // Store fetched articles here
    };
  },
  computed: {
    filteredArticles() {
      return this.articles.filter((article) => {
        const title = article.title || ""; // Fallback to empty string if title is null or undefined
        const description = article.description || ""; // Fallback to empty string if description is null or undefined

        return (
          title.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
          description.toLowerCase().includes(this.searchTerm.toLowerCase())
        );
      });
    },
  },

  watch: {
    searchTerm() {
      // You can choose to update the articles here as well if needed
    },
  },
  mounted() {
    // Fetch news articles when the component is mounted (page is loaded)
    this.fetchNewsArticles();
  },
  methods: {
    async fetchNewsArticles() {
      try {
        const apiKey = "7978ef51053f44f3a966ce037e063b01"; // Replace with your actual API key
        const response = await axios.get(
          `https://newsapi.org/v2/top-headlines?country=us&apiKey=${apiKey}`
        );

        if (response.data.articles) {
          this.articles = response.data.articles;
        }
      } catch (error) {
        console.error("Error fetching articles:", error);
      }
    },
    highlightKeywords(text) {
      if (!this.searchTerm || !text) {
        return text;
      }

      const searchTerm = this.searchTerm.toLowerCase();
      const words = text.split(" ");
      const highlightedWords = words.map((word) => {
        const lowercaseWord = word.toLowerCase();
        if (lowercaseWord.includes(searchTerm)) {
          return `<span class="highlight">${word}</span>`;
        } else {
          return word;
        }
      });
      return highlightedWords.join(" ");
    },
  },
};
</script>

<style>
/* Container styles */
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

/* Page title styles */
.page-title {
  font-size: 24px;
  margin-bottom: 20px;
}

/* Search input styles */
.search-input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 20px;
}

/* Article list styles */
.article-list {
  display: flex;
  flex-direction: column;
}

/* Individual article styles */
.article {
  background-color: #f5f5f5;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  margin-bottom: 10px;
}

/* Article title styles */
.article-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 5px;
}

/* Article description styles */
.article-description {
  font-size: 14px;
}

/* Highlighted search term styles */
.highlight {
  background-color: yellow;
}
</style>
