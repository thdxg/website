<script lang="ts">
  import { onMount } from "svelte";
  import { glitch } from "$lib/glitch";
  import "./layout.css";

  let { children } = $props();

  onMount(() => glitch({ initial: { density: 0.4 } }));
</script>

<svelte:head>
  <title>Ethan Lee</title>
</svelte:head>

{@render children()}
