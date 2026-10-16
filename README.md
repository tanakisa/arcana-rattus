# Arcana Rattus

An interactive, stream-ready tarot card pull overlay built with Svelte and Vite.

## Development

Install dependencies with `npm install`, then start the local server with `npm run dev`.
Use `npm run build` to create a production build in `dist/`.

## Card artwork

Place card-front artwork in `public/cards/` and set each card's `image` value in
`src/data/cards.js` to its public path (for example, `/cards/scavenger.webp`).
The card name and subtle tarot counterpart are overlaid on top of the artwork,
so the image can contain only the logo. The image slot stays empty until an
image path is provided. The deck back is decorative CSS and intentionally has
no logo.

Each draw has a 20% chance of being reversed. The card is shown upside down and
uses its `reversed` keywords; otherwise it uses `upright` keywords.
