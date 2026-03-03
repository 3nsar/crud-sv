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


  <input
    bind:value={title}
    placeholder="Titel..."
    class="title"
  />

  <textarea
    bind:value={content}
    placeholder="Write something..."
    class="area-text"
  ></textarea>

  <button class="create-btn" on:click={createPost}>
    Create Post
  </button>
</div>


<div class="posts-content">
<ul>
  {#each posts as post (post.id)}
  <div class="posts-div">
    <li style="margin-bottom: 10px;">
      <span class="post-title">{post.title}</span>
      <br />
      <span>{post.content}</span>
      <br />

      <button on:click={() => updatePost(post.id)}>
        ✏️ Edit
      </button>

      <button on:click={() => deletePost(post.id)}>
        ❌ Delete
      </button>
    </li>
    </div>
  {/each}
</ul>
</div>
<a class="btn-back" href="/">go back</a>
</div>

<style  lang="scss">

.btn-back {
  background-color: black;
  text-decoration: none;
  color: #c75d06;
  font-weight: bold;
  padding: 10px 20px;
  border-radius: 10px;
}

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
  margin-right: 40px;

  .title{
      margin-top: 10px;
     width: 300px;
    height: 50px;
    color:black;
    font-weight: bold;
    font-size: 20px;
  }
  .area-text{
    margin-top: 10px;
    width: 300px;
    height: 300px;
    color:black;
    font-weight: bold;
    font-size: 20px;
  }
  .posts-div{
    background-color: #02013b;
    padding: 20px;
    width: 400px;
    margin: 10px 0 0 0;

    button{
      margin-top: 20px;
      background-color: black;
      padding: 5px;
      border-radius: 5px;
      border: none;
      cursor: pointer;
    }
  }

  .post-title{
    font-size: 20px;
    font-weight: bold;
  }


  .create-btn{
    margin-top: 10px;
     cursor: pointer;
    background-color: #c75d06;
    padding: 12px;
    font-size: 20px;
    border: none;
    font-weight: 700;
    border-radius: 20px;
    @media (max-width: 600px) {
      font-size: 15px;
    }
  }
}

</style>