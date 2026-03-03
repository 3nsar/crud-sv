<script lang="ts">
  import { onMount } from 'svelte';
  import { supabase } from '$lib/supabase';


  // -----------------------
  // Type Definition
  // -----------------------
  type Post = {
    id: number;
    title: string;
    content: string;
    created_at: string;
  };

  let posts: Post[] = [];
  let title: string = '';
  let content: string = '';
  let loading: boolean = false;

  // -----------------------
  // READ
  // -----------------------
  async function loadPosts(): Promise<void> {
    loading = true;

    const { data, error } = await supabase
      .from('posts')
      .select('*')
      .order('created_at', { ascending: false });

    if (error) {
      console.error('Fehler beim Laden:', error.message);
      posts = [];
    } else {
      posts = data ?? [];
    }

    loading = false;
  }

  // -----------------------
  // CREATE
  // -----------------------
  async function createPost(): Promise<void> {
    if (!title || !content) return;

    const { error } = await supabase
      .from('posts')
      .insert([{ title, content }]);

    if (error) {
      console.error('Fehler beim Erstellen:', error.message);
      return;
    }

    title = '';
    content = '';
    await loadPosts();
  }

  // -----------------------
  // UPDATE
  // -----------------------
  async function updatePost(id: number): Promise<void> {
    const newTitle = prompt('Neuer Titel?');
    if (!newTitle) return;

    const { error } = await supabase
      .from('posts')
      .update({ title: newTitle })
      .eq('id', id);

    if (error) {
      console.error('Fehler beim Updaten:', error.message);
      return;
    }

    await loadPosts();
  }

  // -----------------------
  // DELETE
  // -----------------------
  async function deletePost(id: number): Promise<void> {
    const confirmDelete = confirm('Delete?');
    if (!confirmDelete) return;

    const { error } = await supabase
      .from('posts')
      .delete()
      .eq('id', id);

    if (error) {
      console.error('Fehler beim Löschen:', error.message);
      return;
    }

    await loadPosts();
  }

  onMount(loadPosts);
</script>


<div class="posts-container">

<div class="posts-content">
<h1>Posts</h1>

  <input
    bind:value={title}
    placeholder="Titel"
  />

  <textarea
    bind:value={content}
    placeholder="Inhalt"
  ></textarea>

  <button on:click={createPost}>
    Speichern
  </button>
</div>

{#if loading}
  <p>Lade...</p>
{/if}
<div class="posts-content">
<ul>
  {#each posts as post (post.id)}
    <li style="margin-bottom: 10px;">
      <b>{post.title}</b>
      <br />
      {post.content}
      <br />

      <button on:click={() => updatePost(post.id)}>
        ✏️ Edit
      </button>

      <button on:click={() => deletePost(post.id)}>
        ❌ Delete
      </button>
    </li>
  {/each}
</ul>
</div>
<a href="/">go back</a>
</div>

<style  lang="scss">
.posts-container{
    display: flex;
  align-items: flex-start;
  justify-content: flex-start;
  flex-direction: row;
  min-height: 100vh;
  padding: 50px;
}
.posts-content{
      display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

</style>