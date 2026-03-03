<script>
  import { goto } from '$app/navigation';

  function goToPosts() {
    goto('/posts');
  }


</script>

<div class="container">
  <h1>WELCOME TO FIREPOST🔥</h1>
  <button class="btn1" on:click={goToPosts}>Post something new</button> 
</div>
<style lang="scss">

 @use '../app.scss' as *;

  :global(html, body) {
    margin: 0;
    padding: 0;
    background-color: #01002b;
  }


  .btn1 {
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
</style>