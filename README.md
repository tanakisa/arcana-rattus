# Arcana Rattus

An interactive, stream-ready tarot card pull overlay built with Svelte and Vite.

## Development

Install dependencies with `npm install`, then start the local server with `npm run dev`.
Use `npm run build` to create a production build in `dist/`.

## Card artwork

Place card-front artwork in `public/cards/` and set each card's `image` value in
`src/data/cards.js` to its public path (for example, `/cards/the-cheese.webp`).
The image slot stays empty until an image path is provided. The deck back is
decorative CSS and intentionally has no logo.
