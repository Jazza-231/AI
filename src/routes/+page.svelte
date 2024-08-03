<script lang="ts">
  import { onMount } from "svelte";

  let prompt = "";
  let variations: number = 1;
  let srcs: string[] = [];
  let loading: boolean[] = [];

  const url = "https://image.pollinations.ai/prompt/";

  function generate() {
    if (!variations) return;

    const trimmedPrompt = prompt.trim().replace(/\s+/g, "-");

    srcs = [];
    loading = new Array(variations).fill(true);
    for (let i = 0; i < variations; i++) {
      srcs = [...srcs, `${url}${trimmedPrompt}${i === 0 ? "" : "-" + i}`];
    }

    updateURLParams();
  }

  function handleKeyPress(event: KeyboardEvent) {
    if (event.key === "Enter") {
      generate();
      checkImagesLoaded();
    }
  }

  function handleImageLoad(index: number) {
    loading[index] = false;
  }

  function checkImagesLoaded() {
    setTimeout(() => {
      srcs.forEach((src, index) => {
        const img = new Image();
        img.src = src;

        img.onload = () => {
          handleImageLoad(index);
        };
      });
    }, 100);
  }

  function updateURLParams() {
    const params = new URLSearchParams(window.location.search);
    params.set("prompt", prompt);
    params.set("variations", variations.toString());
    const newUrl = `${window.location.pathname}?${params.toString()}`;
    window.history.replaceState({}, "", newUrl);
  }

  onMount(() => {
    const params = new URLSearchParams(window.location.search);
    const paramPrompt = params.get("prompt");
    const paramVariations = params.get("variations");

    if (paramPrompt) prompt = paramPrompt;
    if (paramVariations) variations = parseInt(paramVariations, 10);

    if (paramPrompt || paramVariations) {
      generate();
    }
  });
</script>

<main>
  <h1>Image Generator</h1>
  <div class="input-container">
    <input
      class="prompt"
      type="text"
      placeholder="Prompt"
      bind:value={prompt}
      on:keypress={handleKeyPress}
    />
    <input
      class="variations"
      type="number"
      placeholder="Variations"
      bind:value={variations}
      on:keypress={handleKeyPress}
    />
    <button on:click={generate}>Generate</button>
  </div>

  <div class="grid">
    {#each srcs as src, i}
      <div class="image-container">
        <div class="loading" class:hidden={!loading[i]}>
          Loading<span class="dots"></span>
        </div>
        <img
          {src}
          alt={src}
          on:load={() => handleImageLoad(i)}
          class:hidden={loading[i]}
        />
      </div>
    {/each}
  </div>
</main>

<style lang="scss">
  :global(body) {
    background-color: #121212;
    color: #ffffff;
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
  }

  main {
    padding: 2rem;
    text-align: center;

    h1 {
      margin-bottom: 1.5rem;
    }

    .input-container {
      display: flex;
      justify-content: center;
      gap: 1rem;
      margin-bottom: 2rem;

      input,
      button {
        padding: 0.5rem;
        border: none;
        border-radius: 4px;
        font-size: 1rem;
      }

      input {
        width: 300px;
        background-color: #121212;
        color: white;
        border: white 2px solid;

        &.variations {
          width: 50px;
        }
      }

      button {
        cursor: pointer;
        background-color: #6200ea;
        color: #ffffff;
        transition: background-color 0.3s;

        &:hover {
          background-color: #3700b3;
        }
      }
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(20rem, 1fr));
      gap: 10px;

      .image-container {
        position: relative;
        width: 100%;
        height: 20rem;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: #333;
        border-radius: 4px;
        overflow: hidden;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);

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

        img {
          max-width: 100%;
          height: auto;
          object-fit: cover;
          display: block;
          transition: transform 0.3s;

          &.hidden {
            display: none;
          }

          &:hover {
            transform: scale(1.05);
          }
        }
      }
    }
  }
</style>
