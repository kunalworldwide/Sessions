This directory is my personal index of talks, slide decks, recordings, and supporting resources.

Use this folder to collect everything related to a presentation: slides, speaker notes, demo code, references, and any handouts.

## Structure

Organize each talk as a top-level folder named with a short slug and year, for example:

```
2024-docker-devtools-day-3/
2023-intro-to-opa/
```

Inside a talk folder, a minimal layout works well:

```
slides/          # slide decks (PDF, PPTX, or reveal.js)
notes/           # speaker notes or scripts
code/            # demo code or examples
assets/          # images, icons, media
references.md    # links and citation info
README.md        # short summary of the talk (audience, duration, tags)
```

## Naming conventions

- Use a short, kebab-case slug with the year prefix: `YYYY-topic-name`.
- Keep filenames descriptive and include the format: `talk-title.slides.pdf`, `demo-01.zip`.

## How to add a new talk

Create a folder, add a short `README.md`, and commit. Example:

```zsh
mkdir -p "2025-your-talk-slug"/{slides,notes,code,assets}
echo "# Your Talk Title" > "2025-your-talk-slug/README.md"
git add "2025-your-talk-slug" && git commit -m "Add folder for 2025 your talk"
```

## Tips

- Keep slide PDFs and a source copy (e.g., Keynote/PPTX) together.
- Put runnable demos in `code/` with a short README explaining how to run them.
- Archive large recordings (video/audio) in `assets/` or an external storage and link from `references.md`.

## License & attribution

Content here follows the license of the original author (usually me). If you include third-party materials, add attribution in `references.md`.

## Contact

If you need to reuse or reproduce anything here, open an issue or contact me.

---

Keep it simple and consistent — this folder should make it easy to find and reuse talk materials later.