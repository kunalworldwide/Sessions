This directory is my personal index of talks, slide decks, recordings, and supporting resources.

Use this folder to collect everything related to a presentation: slides, speaker notes, demo code, references, and any handouts.

## Current workspace snapshot

The repository is organized by year folders. Presently it contains (examples from the repo):

- `2023/`
	- `IND-KOL-EXT-DF23-KUNAL-DAS-DEVOPS.pdf`
- `2024/`
	- `Kubernetes Bangalore Meetup May 2024/`
		- `Containerization at Scale.pdf`
	- `Kubetools Day May 24/`
		- `Introduction to KEDA.pdf`
	- `SRE meetup may24/`
		- `Containerization at Scale.pdf`
- `2025/`
	- `04_MsReactor/`
		- `Containerapps.pdf`
	- `Build with AI @ Amity University/`
		- `Amity_APR_2025_design.pdf`
	- `C3 #1/`
		- `The Future of Kubernetes AI-Driven Management.pdf`
	- `GDG_BLR/`
		- `GCCDBLR-The Future of Kubernetes AI-Driven Management.pdf`
	- `Observability & Optimisation/`
		- `Amity_APR_2025.pdf`

This README documents the current layout and a suggested minimal convention for adding more material.

## Suggested structure for each talk or event

Inside each event/talk folder a minimal layout keeps things discoverable:

```
slides/          # slide decks (PDF, PPTX, or reveal.js)
notes/           # speaker notes or scripts
code/            # demo code or examples
assets/          # images, icons, media and recordings
references.md    # links and citation info
README.md        # short summary of the talk (audience, duration, tags)
```

## Naming conventions (recommended)

- Prefer a short, kebab-case slug with an optional year prefix: `YYYY-topic-name` or `topic-name`.
- Keep filenames descriptive and include the format: `talk-title.slides.pdf`, `demo-01.zip`.
- For folders with spaces or special chars (already present here) that's fine; use backticks or quoting when scripting.

## How to add a new talk (quick)

From a zsh shell, a simple example to create a new talk folder is:

```
mkdir -p "2025-your-talk-slug"/{slides,notes,code,assets}
echo "# Your Talk Title" > "2025-your-talk-slug/README.md"
git add "2025-your-talk-slug" && git commit -m "Add folder for 2025 your talk"
```

If you prefer a single-year flat layout, create a top-level folder like `2026/` and add events underneath it.

## Practical tips

- Keep source slide files (Keynote/PPTX) alongside exported PDFs.
- Put runnable demos in `code/` with a README explaining how to run them.
- For very large recordings, keep them in `assets/` or external storage and add a link in `references.md`.

## License & attribution

Content here follows the license of the original author (you). For third-party materials add attribution in `references.md`.

## Contact

If you want changes to the layout or want me to reorganize files, tell me which year or folders to change and I'll help.

---

Keep it simple and consistent — this folder should make it easy to find and reuse talk materials later.