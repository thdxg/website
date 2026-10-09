<script lang="ts">
  import "$lib/styles/tokens.css";
  import "$lib/styles/bundle.css";

  let { children } = $props();
</script>

<svelte:head>
  <title>Ethan Lee</title>
</svelte:head>

<div class="ec-page" style:--width-page="640px">
  {@render children()}

  <footer class="ec-footer">
    <span>© {new Date().getFullYear()} Ethan Lee</span>
  </footer>
</div>
