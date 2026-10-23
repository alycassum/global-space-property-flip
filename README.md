# Flip House — Global Space × Vision Next

A small, responsive browser game prototype about buying a neglected property, choosing renovation finishes, and selling at a profit. Built with plain HTML, CSS and JavaScript so it is easy to understand and publish.

## Play locally

### Easiest method
1. Download or unzip this project folder.
2. Open `index.html` in Chrome, Edge, Firefox or Safari.
3. Start playing.

### Optional local web server
If Python is installed, open a terminal in this folder and run:

```bash
python -m http.server 8000
```

Then visit `http://localhost:8000`.

## Publish on GitHub Pages

1. Sign in to GitHub and create a new repository, for example `global-space-property-flip`.
2. For the simplest first release, make the repository **Public**.
3. Upload `index.html`, `styles.css`, `game.js`, and `README.md` to the repository root (not inside an extra nested folder).
4. Open **Settings → Pages**.
5. Under **Build and deployment**, choose **Deploy from a branch**.
6. Select branch `main` and folder `/ (root)`, then click **Save**.
7. Wait a minute or two and refresh the Pages settings. GitHub will show the published URL, usually `https://YOUR-USERNAME.github.io/global-space-property-flip/`.
8. Open the URL on desktop and mobile to test it.

If you edit the files later and commit the changes to `main`, GitHub Pages will publish the update automatically. GitHub's Pages features for private repositories depend on your plan; a public repository is the straightforward free route for this prototype.

## Project structure

```text
global-space-property-flip/
├── index.html   # Game structure and content
├── styles.css   # Premium muted visual style and responsive layout
├── game.js      # Game state, property data, renovation choices and calculations
└── README.md    # Local run and deployment instructions
```

## Prototype rules

- Starting cash: PKR 1 crore (game currency).
- Buy a property, then choose one finish level for the living room, kitchen and bathroom.
- Finish choices trade off cost against simulated resale value.
- Accept the sale offer to see profit or loss and unlock the next property.
- Game values are fictional and exclude real-world taxes, financing, agent fees and transaction costs.

## Notes

- This version has no accounts, backend, database or external game engine.
- Google Fonts are loaded from a CDN when internet access is available; system fallbacks keep the game usable offline.
- The current character art is a stylised placeholder rather than a portrait of a real person. Replace it with approved brand artwork if you want a closer likeness of Aly.
