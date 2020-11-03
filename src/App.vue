<script>
import dayjs from "dayjs";
const profileURL = "https://dev.to/api/users/by_username?url=";
const articlesURL = "https://dev.to/api/articles?per_page=5&username=";

export default {
  name: "App",
  data() {
    return {
      username: "",
      data: false,
      articleList: "",
      github: "",
      stats: "",
    };
  },
  methods: {
    fetchData(e) {
      e.preventDefault();
      this.fetchInfo(this.username.toLowerCase());
    },
    async fetchInfo(x) {
      const res = await fetch(`${profileURL}${x}`)
        .then(async (data) => {
          let d = await data.json();
          this.github = d.github_username;
          if (this.github) {
            this.taData = `# ${d.name} \n ${d.summary} \n <hr>\n\n### 📝 Latest articles from [dev.to](https://dev.to/${d.username})\n\n`;
          }
          return fetch(`${articlesURL}${x}`);
        })
        .then(await fetch(`${articlesURL}${x}`))
        .then(async (article) => {
          let k = await article.json();
          k.forEach((element) => {
            let date = dayjs(element.published_timestamp).format("MMM DD YYYY");
            this.articleList += `* ${date} [${element.title}](${element.url}) \n`;
          });
          this.taData += this.articleList;
          if (this.github) {
            this.stats = `<p align="center">\n\n<img src="https:\/\/visitor-badge.laobi.icu\/badge?page_id=${this.github}.${this.github}" />\n\n<img src="https:\/\/img.shields.io\/badge\/dynamic\/json?color=brightgreen&label=followers&query=followers&url=https://api.github.com/users/${this.github}" />\n\n</p>`;
            this.taData += this.stats;
          }
          this.data = true;
        });
    },
    copy() {
      var copyText = document.querySelector("#mdData");
      copyText.select();
      document.execCommand("copy");
    },
  },
};
</script>

<style>
textarea {
  width: 100%;
  min-height: 400px;
}
code {
  color: crimson;
}
input {
  width: 15em;
}
</style>

<template>
  <main>
    <form>
      <label for="username"></label>
      <input
        v-model="username"
        type="text"
        id="username"
        placeholder="dev.to username or type ben"
        required
      />
      <button type="submit" @click="fetchData" :disabled="username == ''">
        Fetch Data
      </button>
    </form>

    <div v-if="data">
      <p class="py-2">
        Copy this to your github profile's <code>README.md</code>
      </p>
      <textarea name="md" id="mdData" v-model="taData"> </textarea>
      <button @click="copy">
        <svg
          title="Copy to clipboard"
          class="octicon octicon-clippy"
          viewBox="0 0 14 16"
          version="1.1"
          width="14"
          height="16"
          aria-hidden="true"
        >
          <path
            fill-rule="evenodd"
            d="M2 13h4v1H2v-1zm5-6H2v1h5V7zm2 3V8l-3 3 3 3v-2h5v-2H9zM4.5 9H2v1h2.5V9zM2 12h2.5v-1H2v1zm9 1h1v2c-.02.28-.11.52-.3.7-.19.18-.42.28-.7.3H1c-.55 0-1-.45-1-1V4c0-.55.45-1 1-1h3c0-1.11.89-2 2-2 1.11 0 2 .89 2 2h3c.55 0 1 .45 1 1v5h-1V6H1v9h10v-2zM2 5h8c0-.55-.45-1-1-1H8c-.55 0-1-.45-1-1s-.45-1-1-1-1 .45-1 1-.45 1-1 1H3c-.55 0-1 .45-1 1z"
          ></path>
        </svg>
        Copy
      </button>
    </div>
  </main>
  <footer class="text-center">
    <span>from</span> <a href="https://anoram.com">Anoram</a>
  </footer>
</template>
