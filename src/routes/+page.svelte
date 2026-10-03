<script>
  import homelabImg from "$lib/assets/homelab.png?enhanced";
  let social = [
    { name: "github", href: "https://github.com/thdxg" },
    { name: "linkedin", href: "https://linkedin.com/in/ethantlee" },
    {
      name: "resume",
      href: "/resume",
    },
  ];
</script>

<header class="glitch mb-12 space-y-4">
  <img src="/favicon.svg" alt="logo" class="size-10 shrink-0" />
  <h1 class="glitch-initial">Ethan Lee</h1>
  <nav class="flex gap-4">
    {#each social as link}
      <a href={link.href} target="_blank" class="underline underline-offset-3"
        >{link.name}</a>
    {/each}
  </nav>
</header>

<main class="glitch space-y-12">
  <section id="intro">
    <p>
      terminal lover, student, oss maintainer
      <br />
      building things in web and cloud
    </p>
  </section>

  <section id="homelab" class="flex flex-col gap-4">
    <div class="img-bg size-50 shrink-0">
      <enhanced:img
        src={homelabImg}
        alt="homelab"
        class="size-full object-cover" />
    </div>
    <p>
      this is my bare-metal k8s cluster
      <br />
      running my projects like this website
      <br />
      see
      <a
        href="https://grafana.thdxg.dev/public-dashboards/1f9aad71e31f46c3a5bdf067b1022fd4"
        target="_blank">
        dashboard
      </a>,
      <a href="https://github.com/thdxg/homelab" target="_blank">github</a>
    </p>
  </section>

  <section id="bio">
    <ul class="tree">
      <li><span class="marker">*</span>things i do</li>
      <li>
        <span class="marker">⎿</span>
        swe intern @ tesla
      </li>
      <li>
        <span class="marker">⎿</span>
        master of software engineering @ cmu
      </li>
      <li>
        <span class="marker">⎿</span>
        contributing to kubernetes, next.js, echo
      </li>
    </ul>
  </section>

  <section id="experience">
    <ul class="tree">
      <li><span class="marker">*</span>things i've done</li>
      <li>
        <span class="marker">⎿</span>
        founding swe @ huddle surety (2025 - 2026)
      </li>
      <li>
        <span class="marker">⎿</span>
        swe intern @ ukg (2025)
      </li>
      <li>
        <span class="marker">⎿</span>
        swe intern @ estreamly (2024)
      </li>
    </ul>
  </section>

  <section id="projects">
    <ul class="tree">
      <li><span class="marker">*</span>things i've built</li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/macterm" target="_blank">macterm</a>
        — a lightweight macos terminal with vertical tabs, session persistence, and
        native ui
      </li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/homelab" target="_blank">homelab</a>
        — personal datacenter; a kubernetes cluster on raspberry pi
      </li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/helix" target="_blank">helix</a>
        — a custom fork of helix editor with plugins, file reloading, and image/pdf
        preview
      </li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/llog" target="_blank">llog</a>
        — a fully local journaling cli written in go
      </li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/ttype" target="_blank">ttype</a>
        — a simple bring-your-own-text typing test cli written in rust
      </li>
      <li>
        <span class="marker">⎿</span>
        <a href="https://github.com/thdxg/ghfetch" target="_blank">ghfetch</a>
        — neofetch for github profiles written in go
      </li>
    </ul>
  </section>
</main>
