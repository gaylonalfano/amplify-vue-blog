<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <h1>Blog Home</h1>
    <button @click="createPost">Add Post</button>
    <div v-if="postsRef">
      <div v-for="post in postsRef" :key="post.id">
        <h3>{{ post.title }}</h3>
        <p>{{ post.content }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watchEffect } from "vue";
import { DataStore } from "@aws-amplify/datastore";
import { Post } from "@/models";

export default defineComponent({
  name: "Home",
  setup() {
    const postsRef = ref<Post[] | null>(null);
    const postRef = ref<Post | null>(null);

    async function queryPost() {
      // NOTE Can use Refs to assign to db response
      // const posts = await DataStore.query(Post);
      postsRef.value = await DataStore.query(Post);
      console.log(postsRef);
      // postsRef.value = posts;
    }

    async function createPost() {
      postRef.value = await DataStore.save(
        new Post({
          title: window.prompt("Blog post title: ") as string,
          blogID: "a3f4095e-39de-43d2-baf4-f8c16f0f6f4d",
          comments: [],
          content: window.prompt("Post content: ") as string,
          authorID: (Math.random() * 100).toString()
        })
      );
      console.log(postRef.value);
    }

    watchEffect(async () => {
      await queryPost();
    });

    return { createPost, postsRef, postRef, queryPost };
  }
});
</script>
