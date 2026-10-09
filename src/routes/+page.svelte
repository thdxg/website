<script lang="ts">
  import { animate, motionValue, type AnimationOptions } from "motion";
  import BriefcaseIcon from "phosphor-svelte/lib/BriefcaseIcon";
  import CodeIcon from "phosphor-svelte/lib/CodeIcon";

  const reduceMotion = () =>
    matchMedia("(prefers-reduced-motion: reduce)").matches;
  // Under Reduce Motion everything jumps straight to its end state.
  const motion = (options: AnimationOptions): AnimationOptions =>
    reduceMotion() ? { duration: 0 } : options;
  // The one slide both highlights use: quick, easing out.
  const glide = {
    duration: 0.2,
    ease: [0.2, 0.8, 0.2, 1] as [number, number, number, number],
  };

  const links = [
    { name: "GitHub ↗", href: "https://github.com/thdxg" },
    { name: "LinkedIn ↗", href: "https://linkedin.com/in/ethantlee" },
    { name: "Resume", href: "/resume" },
  ];

  const experience = [
    { title: "SWE intern, Tesla", year: 2026 },
    { title: "Founding SWE, Huddle Surety", year: 2025 },
    { title: "SWE intern, UKG", year: 2025 },
    { title: "SWE intern, eStreamly", year: 2024 },
  ];

  // Each entry gets an equal slice of the timeline, newest at the top. The
  // marker moves freely; the entry is whichever slice it is in.
  const DIGITS = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
  const clamp = (n: number, min: number, max: number) =>
    Math.min(Math.max(n, min), max);
  const centerOf = (i: number) => (i + 0.5) / experience.length;

  // Positions run 0 (top) to 1 (bottom). The marker, and the lit text with
  // it, glide toward the pointer.
  let target = centerOf(0);
  const marker = motionValue(centerOf(0));
  let at = $state(marker.get());
  $effect(() => marker.on("change", (v) => (at = v)));
  const moveTo = (to: number) => {
    target = to;
    if (reduceMotion()) marker.jump(to);
    else animate(marker, to, glide);
  };

  const current = $derived(
    clamp(Math.floor(at * experience.length), 0, experience.length - 1),
  );

  // Entries fade a little with distance from the marker: neighbors stay
  // whole, and each step past them loses a fifth.
  const fade = (i: number) => {
    const distance = Math.abs(centerOf(i) - at) * experience.length;
    return Math.max(0.4, 1 - 0.2 * Math.max(0, distance - 1));
  };

  // Each year digit rolls through a column of 0–9 to its new value.
  const DIGIT_HEIGHT = 16;
  function roll(node: HTMLElement, digit: number) {
    animate(node, { y: digit * -DIGIT_HEIGHT }, { duration: 0 });
    return {
      update(next: number) {
        animate(
          node,
          { y: next * -DIGIT_HEIGHT },
          motion({ type: "spring", visualDuration: 0.4, bounce: 0.15 }),
        );
      },
    };
  }

  let timeline: HTMLElement;
  let track: HTMLElement;

  // The marker stops at the centers of the first and last entries.
  function seek(e: PointerEvent) {
    const rect = track.getBoundingClientRect();
    moveTo(
      clamp(
        (e.clientY - rect.top) / rect.height,
        centerOf(0),
        centerOf(experience.length - 1),
      ),
    );
  }

  // The whole section follows the pointer. A touch drags only from the line,
  // so swiping over the entries still scrolls the page; tapping one picks it.
  function grab(e: PointerEvent) {
    if (e.pointerType !== "touch" || track.contains(e.target as Node))
      timeline.setPointerCapture(e.pointerId);
    seek(e);
  }

  // Leaving the section returns the marker and the lit text to the most recent
  // entry. A touch also "leaves" when the finger lifts, so a
  // tapped entry stays picked.
  function release(e: PointerEvent) {
    if (e.pointerType === "touch") return;
    moveTo(centerOf(0));
  }

  // Keys step from entry to entry.
  function step(e: KeyboardEvent) {
    const by: Record<string, number> = {
      ArrowUp: -1,
      ArrowLeft: -1,
      ArrowDown: 1,
      ArrowRight: 1,
    };
    let next: number;
    if (e.key === "Home") next = 0;
    else if (e.key === "End") next = experience.length - 1;
    else if (e.key in by)
      next = clamp(
        Math.round(target * experience.length - 0.5) + by[e.key],
        0,
        experience.length - 1,
      );
    else return;
    moveTo(centerOf(next));
    e.preventDefault();
  }

  const projects = [
    {
      name: "macterm",
      href: "https://macterm.thdxg.dev",
      desc: "A lightweight macOS terminal with vertical tabs, persistent sessions, and native experience",
    },
    {
      name: "homelab",
      href: "https://headlamp.thdxg.dev",
      desc: "Kubernetes homelab on Raspberry Pis",
    },
    {
      name: "eyesclosed",
      href: "https://eyesclosed.thdxg.dev",
      desc: "A low-chroma dark theme for terminals and editors",
    },
    {
      name: "helix",
      href: "https://github.com/thdxg/helix",
      desc: "A custom fork of the Helix editor with plugins, file watching, image rendering, and more",
    },
    {
      name: "llog",
      href: "https://github.com/thdxg/llog",
      desc: "A fully local journaling CLI written in Go",
    },
    {
      name: "ttype",
      href: "https://github.com/thdxg/ttype",
      desc: "A simple bring-your-own-text typing test CLI written in Rust",
    },
    {
      name: "ghfetch",
      href: "https://github.com/thdxg/ghfetch",
      desc: "Neofetch for GitHub profiles, written in Go",
    },
  ];

  // One highlight behind the project list that slides to the hovered or
  // focused row, resizing to fit it, and fades in and out.
  let highlight: HTMLElement;
  let highlightShown = false;

  function moveHighlight(e: Event) {
    const row = e.currentTarget as HTMLElement;
    // Appearing from hidden: jump to the row instead of sliding from the last one.
    animate(
      highlight,
      {
        x: row.offsetLeft,
        y: row.offsetTop,
        width: row.offsetWidth,
        height: row.offsetHeight,
      },
      highlightShown ? motion(glide) : { duration: 0 },
    );
    animate(highlight, { opacity: 1 }, motion({ duration: 0.15 }));
    highlightShown = true;
  }

  function hideHighlight() {
    highlightShown = false;
    animate(highlight, { opacity: 0 }, motion({ duration: 0.15 }));
  }
</script>

<main>
  <section class="ec-hero">
    <h1>Ethan Lee</h1>
    <p class="ec-hero-lede">Building things in terminal and web</p>
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
    <div class="ec-section-body ec-span">
      <h2 class="section-label">
        <BriefcaseIcon size={16} weight="light" aria-hidden="true" />
        <span class="sr-only">Experience</span>
      </h2>
      <div
        class="timeline"
        bind:this={timeline}
        role="presentation"
        onpointerdown={grab}
        onpointermove={seek}
        onpointerleave={release}>
        <ul class="entries">
          {#each experience as entry, i (entry.title)}
            <li
              class="entry"
              style:opacity={fade(i)}
              style:--band="{(at * experience.length - i) * 100}%">
              <span class="ec-entry-title">{entry.title}</span>
            </li>
          {/each}
        </ul>
        <div
          class="track"
          bind:this={track}
          role="slider"
          tabindex="0"
          aria-label="Experience"
          aria-orientation="vertical"
          aria-valuemin={0}
          aria-valuemax={experience.length - 1}
          aria-valuenow={current}
          aria-valuetext={experience[current].title}
          onkeydown={step}>
          <span class="marker" style:top="{at * 100}%"></span>
          <span class="year" style:top="{at * 100}%" aria-hidden="true">
            {#each String(experience[current].year) as digit, i (i)}
              <span class="digit">
                <span class="reel" style:--d={digit} use:roll={Number(digit)}>
                  {#each DIGITS as n (n)}<span>{n}</span>{/each}
                </span>
              </span>
            {/each}
          </span>
        </div>
      </div>
    </div>
  </section>

  <section class="ec-section">
    <div class="ec-section-body ec-span">
      <h2 class="section-label">
        <CodeIcon size={16} weight="light" aria-hidden="true" />
        <span class="sr-only">Projects</span>
      </h2>
      <div class="projects">
        <span class="highlight" bind:this={highlight} aria-hidden="true"></span>
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
  /* Project descriptions in the system's small style (14/22). */
  .ec-entry-desc {
    font-size: 14px;
    line-height: 22px;
  }

  /* Section labels are dim Phosphor icons above each section; the name stays
     for screen readers. */
  .ec-section-body > .section-label {
    display: flex;
    color: var(--inlay);
  }
  .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    overflow: hidden;
    clip-path: inset(50%);
    white-space: nowrap;
  }

  /* Experience: a thin vertical timeline at the column's right edge, newest at
     the top. Pointing anywhere in it moves the marker along the line. */
  .timeline {
    display: grid;
    grid-template-columns: minmax(0, 1fr) var(--space-8);
    column-gap: var(--space-3);
    height: 176px;
    margin-right: calc(-1 * var(--space-4));
    cursor: default;
  }
  .track {
    position: relative;
    touch-action: none;
  }
  .track:focus-visible {
    outline-offset: 0;
  }
  .track::before {
    content: "";
    position: absolute;
    top: 0;
    bottom: 0;
    left: 50%;
    width: 1px;
    transform: translateX(-50%);
    background: var(--rule-strong);
  }
  .marker {
    position: absolute;
    left: 50%;
    width: 7px;
    height: 7px;
    border-radius: var(--radius-pill);
    background: var(--ink-bright);
    box-shadow: 0 0 0 2px var(--page);
    transform: translate(-50%, -50%);
    pointer-events: none;
  }
  /* The year sits just left of the line, level with the marker. */
  .year {
    position: absolute;
    right: calc(50% + var(--space-3));
    font-size: 12px;
    line-height: 16px;
    color: var(--ink-faint);
    font-variant-numeric: tabular-nums;
    transform: translateY(-50%);
    pointer-events: none;
    display: flex;
  }
  /* Each digit is a column of 0–9 that Motion rolls to its value, like an
     odometer; --d places it before the script runs. */
  .digit {
    height: 16px;
    overflow: hidden;
  }
  .reel {
    display: flex;
    flex-direction: column;
    transform: translateY(calc(var(--d) * -16px));
  }
  /* Every entry, each beside its own equal slice of the line. The text is
     muted except for a hard-edged bright block, one row tall, centered on the
     marker, so as the marker moves between rows the block slides through the
     letters: half of one title lit and half of the next. --band is the
     marker's height within the row (0% at its top, 100% at its bottom). */
  .entries {
    display: grid;
    grid-auto-rows: minmax(0, 1fr);
    margin: 0;
    padding: 0;
    list-style: none;
  }
  .entry {
    display: flex;
    align-items: center;
    background: linear-gradient(
      var(--ink-faint) calc(var(--band) - 50%),
      var(--ink-bright) 0 calc(var(--band) + 50%),
      var(--ink-faint) 0
    );
    -webkit-background-clip: text;
    background-clip: text;
  }
  .entry > .ec-entry-title {
    color: transparent;
  }

  /* A whole project row is the link, sized to its content so the highlight hugs it. */
  .ec-entry-link {
    width: fit-content;
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
  }
</style>
