<script>
  import { cards } from "../data/cards.js";

  const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)");
  let currentCard = null;
  let lastCardIndex = -1;
  let pulling = false;

  function pullCard() {
    if (pulling) return;

    pulling = true;
    let index = Math.floor(Math.random() * cards.length);
    if (cards.length > 1 && index === lastCardIndex) {
      index = (index + 1 + Math.floor(Math.random() * (cards.length - 1))) % cards.length;
    }
    lastCardIndex = index;

    window.setTimeout(() => {
      currentCard = cards[index];
      pulling = false;
    }, reducedMotion.matches ? 0 : 440);
  }
</script>

<section class="reading" aria-label="Tarot card reading">
  <div class="card-slot">
    <button
      class:shuffling={pulling}
      class="deck"
      type="button"
      aria-label={currentCard ? `Draw another card. Current card: ${currentCard.name}` : "Draw a tarot card from the deck"}
      disabled={pulling}
      on:click={pullCard}
    >
      <span class="stack-edge back" aria-hidden="true"></span>
      <span class="stack-edge front" aria-hidden="true"></span>
      <span class="deck-stack" aria-hidden="true"></span>
    </button>

    {#if currentCard}
      <article class:visible={!pulling} class:shuffling={pulling} class="card-face" aria-label={currentCard.name}>
        <span class="card-ornament" aria-hidden="true"></span>
        {#if currentCard.image}
          <img class="card-image" src={currentCard.image} alt={currentCard.name}>
        {/if}
      </article>
    {/if}
  </div>

  <p class="deck-hint" hidden={currentCard !== null}>
    <span class="hint-star" aria-hidden="true">✦</span>
    <span>Click to draw your card</span>
    <span class="hint-star" aria-hidden="true">✦</span>
  </p>

  <div class:visible={currentCard !== null} class="result" aria-live="polite">
    {#if currentCard}
      <h2 class="card-title">{currentCard.name}</h2>
      <p class="card-keywords">{currentCard.words}</p>
    {/if}
  </div>

  <button
    class:visible={currentCard !== null}
    class="draw-again"
    type="button"
    disabled={currentCard === null || pulling}
    on:click={pullCard}
  >
    Draw again
  </button>
</section>
