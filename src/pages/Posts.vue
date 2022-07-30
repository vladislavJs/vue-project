<template>
  <div>
    <div class="header">
      <MySelect
          v-model="selectedSort"
          :options="sortOptions"

      ></MySelect>
      <MyInput
          v-model="searchString"
          placeholder="Enter to search"
      />
    </div>

    <PostList
        :posts="sortedPostAfterSearch"
        @remove="removePost"
    ></PostList>
    <MyPagination
        :current-page="page"
        :total-pages="totalPages"
        @changePage="changePage"
    >
    </MyPagination>
    <MyButton @click="showDialog">Create new user</MyButton>
    <MyModal v-model:show="modalVisible">
      <PostForm
          @createPost="createPost"
      />
    </MyModal>

  </div>
</template>

<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import MyModal from "@/components/UI/MyModal";
import MyButton from "@/components/UI/MyButton";
import axios from "axios";
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";
import MyPagination from "@/components/UI/MyPagination";

export default {
  components: {
    MyPagination,
    MyInput,
    MySelect,
    MyButton,
    MyModal,
    PostList, PostForm
  },
  data() {
    return {
      posts: [],
      modalVisible: false,
      selectedSort: '',
      sortOptions: [
        {value: 'title', name: 'Name'},
        {value: 'body', name: 'Text'}
      ],
      searchString: '',
      page: 1,
      limit: 10,
      totalPages: 0
    }
  },
  methods: {
    createPost(post) {
      this.posts.push(post)
      this.modalVisible = false
    },

    removePost(post) {
      this.posts = this.posts.filter(p => p.id !== post.id)
    },

    showDialog() {
      this.modalVisible = true
    },

    changePage(pageNumber) {
      this.page = pageNumber
      this.fetchPosts()
    },

    async fetchPosts() {
      const response = await axios.get('https://jsonplaceholder.typicode.com/posts?', {
        params: {
          _limit: this.limit,
          _page: this.page
        }
      })
      this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
      this.posts = response.data
    }
  },
  mounted() {
    this.fetchPosts()
  },
  computed: {
    sortedPost() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedPostAfterSearch() {
      return this.sortedPost.filter(post => post.title.includes(this.searchString))
    }
  }
}
</script>

<style>

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 1rem 0;
}



</style>