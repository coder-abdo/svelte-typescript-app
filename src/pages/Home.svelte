<style>
.ml-2 {
  margin-left: 2rem;
}
.py-5 {
  padding-top: 5rem;
  padding-bottom: 5rem;
}
</style>

<script lang="ts">
import { onMount } from "svelte";
import Loader from "../components/Loader.svelte";
import PostForm from "../components/PostForm.svelte";
interface IPost {
  createdAt?: string;
  id: string | null;
  userId?: number | string;
  body: string;
  title: string;
}
let editingPost: IPost = {
  id: null,
  title: "",
  body: "",
};
const baseUrl = "https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev";
let posts: IPost[] = [];
onMount(async () => {
  const data = await fetch(baseUrl + "/posts");
  posts = await data.json();
  // console.log(posts)
});
function addPost({ detail: post }: { detail: IPost }) {
  if (posts.find((p) => p.id === post.id)) {
    const index = posts.findIndex((p) => p.id === post.id);
    let updatedPosts = posts;
    updatedPosts.splice(index, 1, post);
    posts = updatedPosts;
  } else {
    posts = [post, ...posts];
  }
}
function updatePost(post: IPost): void {
  editingPost = post;
}
async function deletePost(id: string): Promise<void> {
  if (confirm("Are you sure?")) {
    const data = await fetch(`${baseUrl}/post/${id}`, {
      method: "DELETE",
    });
    const res = await data.json();
    if (res) {
      posts = posts.filter((post) => post.id !== id);
    }
  }
}
</script>

<div class="row py-5">
  <div class="col s6">
    <PostForm on:addPost="{addPost}" editingPost="{editingPost}" />
  </div>
</div>
<div class="row py-5">
  {#if posts.length === 0}
    <Loader />
  {:else}
    {#each posts as post (post.id)}
      <div class="col s12 m6 l3 xl4">
        <div class="card">
          <div class="card-content">
            <h3 class="card-title">{post.title}</h3>
            <p class="flow-text">{post.body}</p>
            <h4 class="text-blue">
              {new Date(post.createdAt).toLocaleDateString()}
            </h4>
          </div>
          <div class="card-action">
            <button
              class="btn orange waves-effect"
              on:click="{() => updatePost(post)}"
            >update</button><button
              class="btn red waves-effect ml-2"
              on:click="{() => deletePost(post.id)}"
            >delete</button>
          </div>
        </div>
      </div>
    {/each}
  {/if}
</div>
