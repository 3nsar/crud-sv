<script>
  import { goto } from '$app/navigation';
  import { supabase } from '$lib/supabase';

  function goToPosts() {
    goto('/posts');
  }


</script>

<div class="container">
  <h1>WELCOME TO FIREPOSTS🔥</h1>
  <button class="" on:click={goToPosts}>View Posts</button> 
</div>

<style  lang="scss">
  @import '../app.scss';
</style>