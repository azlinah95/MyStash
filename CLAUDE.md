# Reading Room Archive

## Project Overview

Reading Room Archive is a personal capture and organization app for people who want to keep recipes, health and lifestyle notes, workouts, spiritual practices, and miscellaneous inspiration together in one private collection. Its core objective is to turn a screenshot or link into a structured, reviewable entry and file it into the right category volume in a digital bookshelf.

The current target is an installable iOS-friendly Progressive Web App (PWA), not a native SwiftUI/Xcode application. Users can add the site to their iPhone Home Screen from Safari. There is no account, cloud sync, server-side storage, or sharing backend yet.

## Tech Stack

- HTML5, CSS3, and browser JavaScript in a single-page app.
- No frontend framework, package manager, transpiler, or build step.
- Browser `localStorage` for private entries, under the key `reading_room_archive_v2`.
- Tesseract.js 5 loaded from jsDelivr for in-browser English OCR of uploaded images. OCR requires network access to load its library and language data.
- Google Fonts loaded remotely: Cormorant Garamond, Inter, and Playfair Display.
- PWA support uses a Web App Manifest and a vanilla JavaScript service worker. There are no npm package versions to install.
- iOS Home Screen metadata and a PNG Apple touch icon are included. Installation and service-worker support require HTTPS, except for localhost development.

## Folder/File Structure

```text
SelfDev_App/
|-- index.html                 App markup, styles, navigation, extraction, and local storage
|-- manifest.webmanifest       PWA name, colors, display mode, start URL, and icon declarations
|-- service-worker.js          Same-origin application-shell caching and offline navigation fallback
|-- CLAUDE.md                  Project overview and developer handoff notes
|-- icons/
|   |-- reading-room-icon.svg  Source book-and-star icon artwork
|   |-- reading-room-192.png   PWA icon (192 x 192)
|   |-- reading-room-512.png   PWA icon (512 x 512)
|   `-- apple-touch-icon.png   iOS Home Screen icon (180 x 180)
`-- .vscode/                   Workspace-specific VS Code settings
```

## Current Progress

- Add Entry supports Link, Image, Video, and Text source modes with five categories: Recipe, Health & Lifestyle, Workout, Spiritual, and Others.
- Uploaded screenshots can be OCR-processed in the browser. Keyword-based category detection proposes a type, and a confirmation dialog allows the user to change the category and title before saving.
- Recipe, wellness, workout, spiritual, and general note parsers format recognized text into category-specific fields. This is rule-based extraction, not a hosted generative AI service.
- The Library opens to five front-facing category covers on a densely arranged bookshelf. Each category opens to its cover and contents; entries open as reading pages.
- Primary navigation is a fixed bottom dock for Add Entry, Library, Recipe Book, and Share. Library is the initial landing view.
- A separate Recipe Book view remains available.
- Archive entries persist in the current browser's localStorage. The service worker caches the same-origin app shell to support offline navigation after the first successful load.
- The current visual direction is restrained black, white, and cream with a classic blackened-wood bookshelf and light category covers. Blue is not part of the palette. Detailed visual polish is deferred until the capture-to-save workflow is functional; keep the basic layout readable and responsive meanwhile. Motion respects `prefers-reduced-motion`.
- There are no automated tests or build scripts. Browser smoke checks have been used during development.

## Product Requirements

- Users must be able to submit a screenshot or a link. Text entry remains a useful fallback.
- AI processing should read screenshot text, extract useful structure, suggest one of the five existing categories, and return a draft with a title and category-specific fields.
- Public, readable web pages are the initial link target. Sign-in-protected, private, blocked, or unreadable pages must produce a clear error or fallback; do not imply that arbitrary links or video content can always be processed. Video transcription is not in the first release scope.
- AI output must remain a draft. Users can review and edit the category, title, and extracted fields, then explicitly confirm before the entry is saved.
- A saved entry must appear in the correct category volume and remain available after reload. Keep saved entries in browser localStorage; there is currently no account, sync, or server-side archive.
- The app must work at phone, tablet, and desktop widths. Basic responsive behavior is part of functional acceptance, but decorative redesign and visual polish happen after the core workflow.
- When visual polish resumes, use only black, white, and cream neutrals. Keep a classic wooden-bookshelf silhouette, category covers visible on the shelf, and a compact arrangement without large unused shelf gaps. Do not reintroduce blue or a colorful/royal treatment.
- AI provider, backend host, usage budget, and exact third-party data-retention terms have not been selected. Before sending private screenshots or page text to an AI service, disclose that transfer and agree on the provider and privacy behavior.

## Functional-First Roadmap

1. **Lock the contract:** Choose the AI provider and backend host; agree on cost limits, upload constraints, supported public-link behavior, privacy disclosure, and the fields required for each category. Prepare representative screenshot and URL examples with expected results.
2. **Build secure ingestion:** Add a small backend endpoint for screenshot and link processing. Keep provider credentials server-side. For URL fetching, allow only safe public HTTP(S) destinations, reject loopback/private-network addresses, re-check redirects, and enforce response-size and timeout limits. Do not retain source files or webpage text on the app backend. Keep the archive in localStorage.
3. **Implement AI extraction:** Read screenshot text and structure with a vision/OCR pipeline. For links, extract readable page content server-side before asking the model to classify and structure it. Validate model output against a fixed schema for Recipe, Health & Lifestyle, Workout, Spiritual, and Others. Return clear errors for unsupported or unreadable input.
4. **Connect review and save:** Show extraction progress, retryable failures, and an editable draft. Do not save automatically. On confirmation, persist the corrected entry locally and update the correct Library volume and entry count.
5. **Verify functional acceptance:** Test representative recipe and workout screenshots and public links; unreadable images; private, invalid, or blocked URLs; malformed AI output; provider timeouts; duplicate confirmation; save/reload/reopen; and browsing saved entries offline. Confirm no failure silently saves partial data and check basic keyboard/accessibility behavior and phone/desktop layouts.
6. **Polish design:** After phase 5 passes, refine the monochrome wooden bookshelf, cover details, responsive spacing, and remaining accessibility and visual details. Do not let this phase block functional work.

**Completion gate:** A user submits a screenshot or readable public link, receives a correctly categorized editable draft, confirms it, reloads the app, and finds the saved entry in the right volume. Invalid or unsupported input must fail clearly without saving.

## Known Issues / Bugs

- OCR, its library, the English language model, and Google Fonts are external network resources. Screenshot recognition cannot run offline, and font loading falls back when offline.
- OCR and category detection are heuristic. Image quality, unusual layouts, other languages, and ambiguous content can produce incomplete or incorrect results; users should review the confirmation before saving.
- Video files are not transcribed. The Video source currently needs a pasted link or accompanying notes; there is no video download/transcription service.
- Entries are local to one browser profile/device. Clearing site data removes the archive; there is no backup/export, account, cloud sync, or multi-user sharing.
- The Share section is a placeholder and does not share entries.
- The service worker only works on a secure origin (HTTPS or localhost); opening `index.html` directly with `file://` is for preview and does not enable installation/offline caching.
- iOS PWA installation is user-initiated through Safari's Share menu. This project does not contain an Xcode project, native Swift code, App Store signing, or native iOS APIs.

## Key Decisions and Reasoning

- **Single-file client app:** The prototype has a small surface area and no backend, so HTML/CSS/JavaScript keeps iteration and local use simple. The manifest and service worker are separate because browsers require separately served PWA resources.
- **PWA for iOS rather than native SwiftUI:** The workspace began as a browser-only app and the target was clarified as an installable web app. This keeps the existing UI and local data model usable on iPhone without pretending the Windows workspace can build/sign an Xcode app.
- **localStorage for the prototype:** It provides immediate private persistence without infrastructure. It is device/browser-specific and must be replaced or supplemented for sync, sharing, or robust backup.
- **Tesseract.js for the prototype:** It runs OCR in the browser without a custom upload server. It is the current heuristic implementation, not the requested AI extraction pipeline; replace or complement it only after provider/privacy decisions are made.
- **Local archive:** Continue storing accepted entries in browser localStorage for the first functional release. AI processing may require sending the submitted source to a third-party provider; that transfer must be disclosed and the provider's retention behavior agreed before implementation.
- **Bookshelf and visual direction:** Keep category-cover, cover, contents, and reading-page navigation. The accepted interim palette is monochrome black/white/cream with a classic dark-wood bookshelf. Prioritize the complete, reliable screenshot/link-to-reviewed-entry workflow before spending time on further visual polish.

## Commands

Run from the project root in PowerShell or another terminal:

```powershell
py -m http.server 8000
```

Then open `http://localhost:8000/` in a browser. If Python is installed under `python` instead of `py`, use `python -m http.server 8000`. Stop the development server with `Ctrl+C`.

For iPhone testing, deploy the site to an HTTPS host or use a secure local-network setup. Open the HTTPS URL in Safari, tap **Share**, then **Add to Home Screen**. The `file://` preview cannot install the PWA or activate the service worker.

There is no build command, test runner, or lint command currently. Smoke-test manually: add a text entry, upload a clear English screenshot, verify the suggested category and extracted fields, save once, confirm persistence after reload, and navigate shelf → cover → contents → entry → back. Check offline shell loading after the first successful load; OCR is expected to remain unavailable offline.
