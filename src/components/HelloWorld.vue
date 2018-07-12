<template>
  <div class="hello">
      <input type="search" placeholder="请随意搜索" @keydown.enter="showResult" v-model="searchVal">
    <ul>
        <li v-for="news in newsList" :key="news.id" @click="getContent(news)">
            <div>  {{news.title}}</div>
        </li>
    </ul>
    <div class="page-content" v-html="pageContent"></div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  data() {
    return {
      searchVal: "",
      newsList: [],
      pageContent: ""
    };
  },
  methods: {
    //显示搜索结果
    showResult() {
      this.newsList = this.newsList.filter((val) => {
        return new RegExp(this.searchVal).test(val.title);
      });
    },
    //请求新闻数据
    getNewsData(param) {
      axios.get(param).then((news) => {
        console.log(news.data);
        this.newsList = news.data.stories;
      });
    },
    //请求新闻页面详情
    getContent(news) {
      const getID = news.id;
      axios.get("/api/4/news/" + getID).then((page) => {

        this.pageContent = `<link rel="stylesheet" href="${page.data.css}">`;
        this.pageContent += page.data.body;
      });
    }
  },
  watch: {
    searchVal() {
      if (this.searchVal === "") {
        this.getNewsData("/api/4/news/latest");
      }
    }
  },
  created() {
    this.getNewsData("/api/4/news/latest");
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input[type="search"] {
  font-size: 18px;
}
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin: 0 10px;
}
a {
  color: #42b983;
}

</style>
