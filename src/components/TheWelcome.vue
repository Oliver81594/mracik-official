<template>
  <div class="data">
    <div v-if="loading" class="loading">
      <h3>Loading...</h3>
    </div>
    <div v-if="error" class="error">
      {{ error }}
    </div>
    <ul v-if="!loading && !error">
      <li v-for="item in items">
        <a :href="item['link']"> {{ item["name"] }}</a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loading: false,
      post: null,
      error: null,
      items: null,
      collection: "Items",
      api_url: "http://127.0.0.1:5000",
      name: "",
      lang: "",
      category: "",
      tags: [],
    }
  },
  async created() {
    this.loading = true;
    const res = await this.latest();
    this.items = res["documents"];
    this.loading = false;
  },
  methods: {
    async postData(name, lang, category, tags) {
      // Default options are marked with *
      let filter = {};
      if (!(name === "")){
          filter["name"] = name;
      }
      if (!(lang === "")){
          filter["lang"] = lang;
      }
      if (!(category === "")){
          filter["category"] = category;
      }
      if (tags.length > 0){
          filter["tags"] = {
            "$all": tags
          };
      }
      let payload = {
        "collection": this.collection,
        "database": "CgqgrsX4",
        "dataSource": "Mracik",
        "filter": filter
      }
      const response = await fetch(this.api_url, {
          method: "POST", // *GET, POST, PUT, DELETE, etc.
          mode: "cors", // no-cors, *cors, same-origin
          cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
          credentials: "same-origin", // include, *same-origin, omit
          headers: {
          "Content-Type": "application/json",
          // 'Content-Type': 'application/x-www-form-urlencoded',
          },
          redirect: "follow", // manual, *follow, error
          referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
          body: JSON.stringify(payload), // body data type must match "Content-Type" header
      });
      return response; // parses JSON response into native JavaScript objects
    },
    async latest() {
      const response = await this.postData(this.name, this.lang, this.category, this.tags);
      const response_1 = await response.text();
      return JSON.parse(response_1);
    }
  },
}
</script>