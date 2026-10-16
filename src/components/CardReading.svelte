<script>
  import { cards } from "../data/cards.js";

  const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)");
  let currentCard = null;
  let currentIsReversed = false;
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
      currentIsReversed = Math.random() < 0.2;
      pulling = false;
    }, reducedMotion.matches ? 0 : 440);
  }

  function resetDeck() {
    if (pulling) return;
    currentCard = null;
    currentIsReversed = false;
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
      <button
        class:visible={!pulling}
        class:shuffling={pulling}
        class:reversed={currentIsReversed}
        class="card-face"
        type="button"
        aria-label={`${currentCard.name}, ${currentIsReversed ? "reversed" : "upright"}`}
        on:click={resetDeck}
      >
        <span class="card-ornament" aria-hidden="true"></span>
        {#if currentCard.image}
          <img class="card-image" src={currentCard.image} alt="">
        {/if}
        <div class="card-heading">
          <span class="card-face-title">{currentCard.name}</span>
          <p class="tarot-counterpart">({currentCard.tarot})</p>
        </div>
      </button>
    {/if}
  </div>

  <p class="deck-hint" aria-live="polite">
    <span class="hint-star" aria-hidden="true">✦</span>
    <span>{currentCard ? `${currentCard.name}${currentIsReversed ? " (rev)" : ""}` : "Click to draw your card"}</span>
    <span class="hint-star" aria-hidden="true">✦</span>
  </p>

  <div class:visible={currentCard !== null} class="result" aria-live="polite">
    {#if currentCard}
      <p class:reversed={currentIsReversed} class="card-orientation">
        {currentIsReversed ? "Reversed" : "Upright"}
      </p>
      <ul class="card-keywords" aria-label={`${currentIsReversed ? "Reversed" : "Upright"} keywords`}>
        {#each currentCard[currentIsReversed ? "reversed" : "upright"] as keyword}
          <li>{keyword}</li>
        {/each}
      </ul>
    {/if}
  </div>
</section>
