<script lang="js">
  import AddPostBtn from "$lib/addPostBtn.svelte";
  import { supabase } from "$lib/supabaseClient";
  import PostCreation from "$lib/postCreation.svelte";
  import Post from "$lib/post.svelte";
  import Sidebar from "$lib/sidebar.svelte";
  import { onMount } from "svelte";
  export let data;

  let session_uuid;
  let user = null;

  onMount(async () => {
    const { data: { session }, error } = await supabase.auth.getSession();

    if (error) {
      console.error("Error fetching session:", error);
    } else if (!session) {
      console.log("No active session. User is not signed in.");
    } else {
      user = session.user;
      session_uuid = user?.id;

      console.log("User:", user);
    }

    console.log(data); 
  });
</script>

<div class="wrapper">
  <div class="add-post-wrapper">
    <AddPostBtn></AddPostBtn>
  </div>

  <Sidebar />

  <div class="posts-wrapper">
    {#each data.posts as post}
      <Post
        logged_in_user_uuid={session_uuid}
        uuid={post.profile_id}
        rating={post.rating}
        desc={post.content}
        song_id={post.song_id}
        likes_arr={post.likes}
        likes_cnt={post.likes_count}
        post_id={post.id}
      ></Post>
    {/each}

    <h2>Load more...</h2>
  </div>
  <PostCreation></PostCreation>
</div>

<style>
  .wrapper {
    display: flex;
    min-height: 90vh;
  }
  .posts-wrapper {
    padding-top: 100px;
    display: inline-block;
    width: 70vw;
  }
  h2 {
    text-align: center;
  }
  .add-post-wrapper {
    position:fixed;
    z-index: 1000;
    right: 2%;
    bottom: 4%;
  }
</style>
