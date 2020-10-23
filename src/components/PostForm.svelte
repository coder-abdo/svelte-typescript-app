<style>
</style>

<script lang="ts">
interface IPost {
  createdAt?: string;
  id: string | null;
  userId?: number | string;
  body: string;
  title: string;
}
import { createEventDispatcher } from "svelte";
import Loader from "./Loader.svelte";
export let editingPost: IPost;
const dispatch = createEventDispatcher();
$: title = editingPost.title;
$: body = editingPost.body;
let loading = false;
const baseUrl =
  "https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev/post";

async function handleSubmit(e: Event) {
  e.preventDefault();
  let url = baseUrl,
    method = "POST";
  if (title.trim() === "" || body.trim() === "") {
    return;
  }
  loading = true;
  const newPost = { title, body };
  if (editingPost.id) {
    url = `${baseUrl}/${editingPost.id}`;
    method = "PUT";
  }
  const data = await fetch(url, {
    method,
    body: JSON.stringify(newPost),
  });
  const post: IPost = await data.json();
  dispatch("addPost", post);
  loading = false;
  editingPost = {
    id: null,
    title: "",
    body: "",
  };
}
</script>

{#if loading}
  <Loader />
{:else}
  <form on:submit="{handleSubmit}">
    <div class="input-field">
      <input
        placeholder="Title"
        id="title"
        type="text"
        class="validate"
        bind:value="{editingPost.title}"
      />
      <label for="title">Title</label>
    </div>
    <div class="input-field">
      <textarea
        placeholder="Body of Post"
        id="body"
        class="materialize-textarea"
        bind:value="{editingPost.body}"
      ></textarea>
      <label for="body">Body</label>
    </div>
    <button
      class="btn waves-effect"
      type="submit"
    >{editingPost.id ? 'update' : 'add'}
      Post</button>
  </form>
{/if}
