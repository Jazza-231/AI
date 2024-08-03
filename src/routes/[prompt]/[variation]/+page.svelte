<script lang="ts">
  import { page } from "$app/stores";

  const slugs = $page.params;

  const baseUrl = "https://image.pollinations.ai/prompt/";
  const src =
    baseUrl +
    slugs.prompt +
    (Number(slugs.variation) === 0 ? "" : "-" + slugs.variation);

  let loading = true;
</script>

<div class="image-container">
  <div class="loading" class:hidden={!loading}>
    Loading<span class="dots"></span>
  </div>

  <a href={src} class:hidden={loading} target="_blank">
    <img {src} alt={src} on:load={() => (loading = false)} />
  </a>
</div>

<style lang="scss">
  .hidden {
    display: none;
  }

  .image-container {
    display: flex;
    justify-content: center;
    align-items: center;

    .loading {
      color: #fff;
      font-size: 1.5rem;

      &.hidden {
        display: none;
      }

      .dots::after {
        content: "";
        display: inline-block;
        animation: dots 1.5s steps(4, end) infinite;
        position: absolute;
      }

      @keyframes dots {
        0%,
        100% {
          content: "";
        }
        25% {
          content: ".";
        }
        50% {
          content: "..";
        }
        75% {
          content: "...";
        }
      }
    }

    a {
      width: 40%;
    }

    img {
      width: 100%;
      border-radius: 4px;
    }
  }
</style>
