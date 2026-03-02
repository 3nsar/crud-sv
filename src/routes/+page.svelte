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
    const confirmDelete = confirm('Wirklich löschen?');
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

<h1>Posts</h1>

<div style="margin-bottom: 20px;">
  <input
    bind:value={title}
    placeholder="Titel"
    style="display:block; margin-bottom:10px;"
  />

  <textarea
    bind:value={content}
    placeholder="Inhalt"
    style="display:block; margin-bottom:10px;"
  ></textarea>

  <button on:click={createPost}>
    Speichern
  </button>
</div>

{#if loading}
  <p>Lade...</p>
{/if}

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