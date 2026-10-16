<script>
  import CanvasSky from "./components/CanvasSky.svelte";
  import CardReading from "./components/CardReading.svelte";
</script>

<svelte:head>
  <title>Arcana Rattus</title>
</svelte:head>

<CanvasSky />

<div class="app">
  <header class="topbar">
    <div class="brand">
      <span class="brand-name">Arcana Rattus</span>
    </div>
  </header>

  <main>
    <p class="eyebrow">A reading from the royal sewers</p>
    <h1>What stirs beneath?</h1>
    <CardReading />
  </main>

  <footer class="footer">
    <a href="https://www.twitch.tv/tanaki_sa" target="_blank" rel="noreferrer">twitch.tv/tanaki_sa</a>
  </footer>
</div>
