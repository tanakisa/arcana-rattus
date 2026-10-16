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
    <p class="eyebrow">A reading from the undercity</p>
    <h1>What stirs beneath?</h1>
    <CardReading />
  </main>

  <footer class="footer">
    <span>Five signs</span><span>One undercity</span><span>Your fate, for now</span>
  </footer>
</div>
