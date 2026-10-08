<script lang="ts">
  const links = [
    { name: "GitHub ↗", href: "https://github.com/thdxg" },
    { name: "LinkedIn ↗", href: "https://linkedin.com/in/ethantlee" },
    { name: "Resume", href: "/resume" },
  ];

  const experience = [
    { title: "SWE intern, Tesla", meta: "Now" },
    { title: "Founding SWE, Huddle Surety", meta: "2025 – Now" },
    { title: "SWE intern, UKG", meta: "2025" },
    { title: "SWE intern, eStreamly", meta: "2024" },
  ];

  const projects = [
    {
      name: "macterm",
      href: "https://macterm.thdxg.dev",
      desc: "A lightweight macOS terminal with vertical tabs, session persistence and native UI.",
    },
    {
      name: "homelab",
      href: "https://headlamp.thdxg.dev",
      desc: "A personal datacenter: a Kubernetes cluster on Raspberry Pi.",
    },
    {
      name: "eyesclosed",
      href: "https://eyesclosed.thdxg.dev",
      desc: "A minimal low-chroma dark theme.",
    },
    {
      name: "helix",
      href: "https://github.com/thdxg/helix",
      desc: "A custom fork of the Helix editor with plugins, file reloading and image and PDF preview.",
    },
    {
      name: "llog",
      href: "https://github.com/thdxg/llog",
      desc: "A fully local journaling CLI written in Go.",
    },
    {
      name: "ttype",
      href: "https://github.com/thdxg/ttype",
      desc: "A simple bring-your-own-text typing test CLI written in Rust.",
    },
    {
      name: "ghfetch",
      href: "https://github.com/thdxg/ghfetch",
      desc: "Neofetch for GitHub profiles, written in Go.",
    },
  ];

  // One highlight behind the project list that slides to the hovered or focused row.
  let highlight = $state({
    top: 0,
    left: 0,
    width: 0,
    height: 0,
    visible: false,
    snap: false,
  });

  function moveHighlight(e: Event) {
    const row = e.currentTarget as HTMLElement;
    // Appearing from hidden: jump to the row instead of sliding from the last one.
    const snap = !highlight.visible;
    highlight = {
      top: row.offsetTop,
      left: row.offsetLeft,
      width: row.offsetWidth,
      height: row.offsetHeight,
      visible: true,
      snap,
    };
    if (snap) {
      requestAnimationFrame(() =>
        requestAnimationFrame(() => (highlight.snap = false)),
      );
    }
  }

  function hideHighlight() {
    highlight.visible = false;
  }
</script>

<main>
  <section class="ec-grid ec-hero">
    <h1>Ethan Lee</h1>
    <p class="ec-hero-lede">Building things for web and terminal</p>
    <div class="ec-hero-actions">
      {#each links as link, i (link.href)}
        {#if i > 0}<span aria-hidden="true">·</span>{/if}
        <a
          class="ec-link ec-link--quiet"
          href={link.href}
          target="_blank"
          rel="external">{link.name}</a>
      {/each}
    </div>
  </section>

  <section class="ec-section">
    <h2 class="ec-section-label">Experience</h2>
    <div class="ec-section-body">
      <ul class="ec-entries">
        {#each experience as entry (entry.title)}
          <li class="ec-entry">
            <span class="ec-entry-title">{entry.title}</span>
            <span class="ec-entry-meta">{entry.meta}</span>
          </li>
        {/each}
      </ul>
    </div>
  </section>

  <section class="ec-section">
    <h2 class="ec-section-label">Projects</h2>
    <div class="ec-section-body">
      <div class="projects">
        <span
          class="highlight"
          class:visible={highlight.visible}
          class:snap={highlight.snap}
          style:transform="translate({highlight.left}px, {highlight.top}px)"
          style:width="{highlight.width}px"
          style:height="{highlight.height}px"
          aria-hidden="true"></span>
        <ul class="ec-entries" onmouseleave={hideHighlight}>
          {#each projects as project (project.href)}
            <li>
              <a
                class="ec-entry ec-entry-link"
                href={project.href}
                target="_blank"
                rel="external"
                onmouseenter={moveHighlight}
                onfocus={moveHighlight}
                onblur={hideHighlight}>
                <span class="ec-entry-title">{project.name}</span>
                <span class="ec-entry-desc">{project.desc}</span>
              </a>
            </li>
          {/each}
        </ul>
      </div>
    </div>
  </section>
</main>

<style>
  /* A whole project row is the link. */
  .ec-entry-link {
    margin: calc(-1 * var(--space-2)) calc(-1 * var(--space-3));
    padding: var(--space-2) var(--space-3);
    border-radius: var(--radius-sm);
    text-decoration: none;
  }

  .projects {
    position: relative;
  }
  .projects > ul {
    position: relative;
  }
  .highlight {
    position: absolute;
    top: 0;
    left: 0;
    border-radius: var(--radius-sm);
    background: var(--surface);
    opacity: 0;
    pointer-events: none;
    transition:
      transform 0.2s cubic-bezier(0.2, 0.8, 0.2, 1),
      width 0.2s cubic-bezier(0.2, 0.8, 0.2, 1),
      height 0.2s cubic-bezier(0.2, 0.8, 0.2, 1),
      opacity 0.15s;
  }
  .highlight.visible {
    opacity: 1;
  }
  .highlight.snap {
    transition: opacity 0.15s;
  }
</style>
