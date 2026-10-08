<script lang="ts">
  import "$lib/styles/tokens.css";
  import "$lib/styles/bundle.css";

  let { children } = $props();
</script>

<svelte:head>
  <title>Ethan Lee</title>
</svelte:head>

<div class="ec-page">
  {@render children()}

  <footer class="ec-footer">
    <span>© {new Date().getFullYear()} Ethan Lee</span>
  </footer>
</div>

<style>
  /* One column at every width: the system's phone layout, kept on desktop. */
  :global(.ec-page) {
    max-width: calc(var(--width-text) + 2 * var(--space-5));
  }
  :global(.ec-page .ec-grid),
  :global(.ec-page .ec-section),
  :global(.ec-page .ec-footer) {
    grid-template-columns: minmax(0, 1fr);
  }
  :global(.ec-page .ec-grid > *),
  :global(.ec-page .ec-section > *),
  :global(.ec-page .ec-footer > *) {
    grid-column: 1;
  }

  /* Section labels sit above their content in the eyebrow type style. */
  :global(.ec-page .ec-section) {
    row-gap: var(--space-3);
  }
  :global(.ec-page .ec-section-label) {
    font-size: 12px;
    line-height: 16px;
    font-weight: 500;
    letter-spacing: 0.04em;
    font-style: italic;
    color: var(--comment);
  }
</style>
