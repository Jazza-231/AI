<script lang="ts">
  import { beforeNavigate, goto } from "$app/navigation";

  beforeNavigate(({ to, cancel }) => {
    if (to?.url.search) return;

    cancel();

    const params = window.location.search;

    goto(to.url + params);
  });
</script>

<h1>
  <a href="/"> Image Generator </a>
</h1>

<slot></slot>

<style lang="scss">
  :global(body) {
    background-color: #121212;
    color: #ffffff;
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
  }

  h1 {
    margin-bottom: 1.5rem;
    text-align: center;

    a {
      color: #ffffff;
      text-decoration: none;
    }
  }
</style>
