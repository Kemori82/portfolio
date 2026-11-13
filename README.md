# Ethan Kreuzer — portfolio

A Cloudflare Worker hosting Ethan's landscape portfolio and its content editor.

## Editing the website


Pictures are resized in your browser and stored in the Cloudflare D1 database. JPEG, PNG, and WebP are supported. Each uploaded result is capped at 900 KB; the picture library is capped at 50 MB. The editor can download a JSON content backup.

## Cloudflare configuration

- Worker: `portfolio`
- Main module: `worker.mjs`
- D1 binding: `DB` (configured in `wrangler.jsonc`)
- Required encrypted Worker secret: `ADMIN_PASSWORD` — use a unique password of at least 16 characters. Set it in Cloudflare; never put it in this repository.
- Deploy command: `npx wrangler deploy`
- Database tables initialize automatically on the first content request.

Keep the Workers **Free** plan. Free-tier limits apply; no paid services or paid overages are enabled by this source. If the account is moved to a paid plan, review its billing settings separately.

The editor uses secure, HttpOnly, SameSite cookies, origin checks, server-side validation, login throttling, and revision checks to prevent accidental overwrites. Rotating ADMIN_PASSWORD signs out existing editor sessions. There is no public password-setup endpoint.

## Files

`worker.mjs` is a self-contained deployment bundle containing the public portfolio, editor, scene artwork, and server handlers. `wrangler.jsonc` declares the Worker and database. `package.json` supplies Wrangler for Cloudflare's GitHub build integration.

Content and uploaded pictures live in D1 and are not overwritten by future code deployments. Use the editor's backup button to export content.

## Visual builder and pages

In `/admin`, the page canvas is always visible. Drag any card's body to move it, or its corner handle to resize it; moving a card automatically switches that page to canvas layout. Click a card to edit it in the **Cards** sidebar. **Pages**, **Design**, and **Settings** organize the remaining controls. On narrow windows, use **Properties** to open or close the sidebar. Use **Preview** to test the page and **Edit** to arrange it. The position inspector also accepts exact coordinates and sizes. Canvas width is 1200 design units; cards stack in saved order on phones. Bring in front / Send behind controls overlapping cards.

Select **Background: drag / scroll** to pan an uploaded background and scroll to zoom it. Precise X/Y, zoom, fit, and fixed/scrolling controls are in Background & display. Switch back to Cards & icons when finished. Drag icons in the preview to offset them; expand their editor rows for size, offsets, order, label, image, and destination. Rows can be dragged to reorder them, or moved with buttons.

**Pages & navigation** adds, duplicates, reorders, or removes pages. New pages use `/p/<address>`. Removing a page moves its cards to Home. Assign existing cards to pages in the Cards editor. Each page has a heading, introduction, browser title, description, navigation visibility, and layout. Home stays at `/`. Up to 20 pages and 100 cards are supported, within a 1.5 MB content limit.

**Favicon & site tab data** uploads a favicon (converted to a transparent 128px PNG) and sets the default title and search description. Page-specific values override these defaults.

Undo/Redo changes your draft. Publish changes publishes it. Download backup / Import backup transfers drafts as JSON. An older editor tab is prevented from saving over the new page structure; download its draft, refresh, and import the backup if it contains unsaved edits.


### Workspace controls

The editor starts in dark mode. **Light editor** switches only the editing workspace, independently of your website colors. The preference is remembered on this browser.

Drag the divider on the left edge of **Site tools** to resize the sidebar. Its keyboard control supports Left/Right (Shift for larger steps), Home, and End. **Hide toolbars** gives the canvas more room; **Show toolbars** restores them. Sidebar width and toolbar visibility are remembered locally.

**Whole page** fits the complete page in the canvas. **Fit width** and **Desktop at 100%** zoom in for editing. **Website preview** opens your current draft without the editor UI; **Back to editor** returns to your work. It does not publish the draft.

Use **Cards → Insert content** to add a picture, text section, downloadable file, or code block to the selected card’s popup. With no selection, a new card is created. Card background pictures remain editable under **Picture**. Code blocks display source as text with an optional language label. They do not run custom scripts. Downloads support PDF, text, Markdown, CSV, JSON, ZIP, and source files up to 900 KB each; larger files can use an external HTTPS link. Images and files share the 50 MB media library. Attachments are served as downloads.

This release requires editor version 6 when publishing, to protect the new content fields from older open editor tabs. Export any unsaved draft using **File → Download backup**, refresh the admin page, then import it if needed.


### Zoom, shortcuts, and rich media

Hold **Ctrl** and scroll over the canvas (including directly over a card) to zoom in or out around the pointer. Ordinary scrolling still pans the page. The toolbar also has **− / + / Fit** controls and a zoom percentage. Choosing a preview size resets the custom zoom. **Ctrl+Z** undoes and **Ctrl+Y** or **Ctrl+Shift+Z** redoes edits, including while the canvas is focused. Uploads and publishing finish before history can change.

The picture pickers now accept animated GIFs up to 900 KB and preserve their frames. Other pictures continue to resize automatically. GIFs can be used for card pictures, popup pictures, icons, and the background. Favicon uploads still become a small PNG.

**Cards → Insert content → Audio / music, Video, or Embed** adds a section to the selected card's popup. Audio/video uploads support MP3, WAV, FLAC, Ogg, M4A, MP4, and WebM, up to 20 MB each, with playback controls and seeking. Formats/codecs still depend on the visitor's browser. Larger media can use a direct HTTPS media-file URL. Videos can have an optional poster picture. Playback is user initiated and stops when the popup or full website preview closes.

For embeds, paste a YouTube, Vimeo, Spotify, or SoundCloud URL or the provider's iframe embed code. Only the iframe URL is used; arbitrary scripts and HTML are not run. Embed height is adjustable. Availability depends on the provider, the content owner's embed settings, and the visitor's browser.

All uploads share the existing 50 MB library. Audio/video is split into small database rows and served with HTTP byte ranges to support seeking within the free Cloudflare setup. No additional hosting service or paid plan is required by this update.


### Card covers and individual page designs

Open a card and expand **Card media: picture, carousel, or video**. Choose a single picture, image carousel, or muted video backdrop. Carousels accept up to 20 pictures with descriptions, ordering, manual arrows, a play/pause control, and adjustable timing. Videos accept the existing 20 MB upload or direct HTTPS media URL and an optional poster image. Automatic playback is optional. The editor starts them paused; use the media controls or website preview to test them. Automatic animation respects the visitor's reduced-motion preference and the page's animation setting. Offscreen videos pause.

**Design now edits only the page selected above the canvas.** Each page saves its own dark/light palette, background, typography, spacing, layout, animation preferences, and page text. Existing pages retain the previous site design until edited. New page designs start from the site's existing defaults. Duplicating a page copies its design, and restoring a design resets only that page. Name/footer and contact links in Settings remain shared across the site.

The background has additional painted strokes and gently rippling circles. Paint strength and circle visibility are adjustable in the current page's Design tab. Disable **Enable animations & background ripples** to make that page static. Reduced-motion preferences also stop the circle animation.


### Standalone text, headers, and Animation

The **Content** tab now starts with a searchable list of the current page's layers. Select one to show its properties. **Focus selected** zooms to that item; **Center page** returns to the whole page. Resizing the sidebar recenters a fitted canvas, and wide property panels keep their controls centered. **Inside a card popup** retains the previous detail-section tools.

**+ Text** adds a standalone text layer. It preserves the visible card arrangement while moving the page into canvas layout. Drag or resize the text like other layers, adjust its typography in Content, or select it and double-click its text to write directly on the canvas. Escape cancels direct editing; Ctrl+Enter finishes it. Pages can have no cards at all. Phone layouts stack text and cards for readability.

**Headers** opens **Pages → Header & page visibility**. The top bar, main page heading, introduction, page navigation, and footer can each be hidden independently. The saved text stays available for later. Individual card headings can also be hidden under Text & behavior.

The **Animation** tab offers fade, rise, side-slide, and soft-zoom entrance effects; duration, start delay, and layer stagger; and typewriter text with adjustable character timing. Apply settings to a page or a selected layer. **Preview animations** opens the website preview. Editing stays static. Effects respect the page's animation setting and reduced-motion preferences. This is a preset animation editor, not a keyframe timeline.

The paint background is one continuous viewport layer and no longer tiles. Background images do not repeat. The editor's mobile layout has a compact publish toolbar and a bottom tools drawer, with touch-friendly controls and scrollable properties. Export any unsaved draft before refreshing an older editor, then import it if needed.

### Standalone components, mastheads, and library effects

**Content → Add to page** adds pictures / animated GIFs, image carousels, video, audio / music, ranked or simple lists, embeds, downloadable files, and display-only code blocks directly to the canvas. No card is required. Select a layer to open its properties, then drag its body or its move handle, resize its corner, or use Position & size. Media controls and links remain interactive; use the move handle to drag those components. Upload limits are unchanged: images / GIFs and downloads up to 900 KB; audio / video up to 20 MB, or a direct HTTPS media URL. Carousels allow 20 images. Lists allow 100 entries, with thumbnails, titles, descriptions, links, and up/down ordering. Code is shown as text, never executed. Embeds accept YouTube, Vimeo, Spotify, and SoundCloud.

**Pages → Header & page visibility** now includes a text or uploaded image / GIF logo; font, color, size, alignment, and horizontal / vertical positioning; header width, height, offsets, sticky positioning, and inline navigation; and independent heading alignment, offsets, and optional font size. Heading text remains in Page heading. These controls apply to the current page. Small-screen headers wrap navigation and reset large offsets to keep it reachable.

**Pages → Add ranked-list page** creates a new page with a main ranked list, a text sidebar, a compact logo/navigation bar, a light palette, and square particles at the edges. It keeps your existing pages. Change the list entries and sidebar text in Content, and customize colors in Design. The desktop columns stack on phones. This is an editable starting layout inspired by the reference, rather than a copy of its branding or content.

**Animation** now uses self-hosted Anime.js 4.5.0 for entrances, with rotate/drop effects, smooth / linear / bounce / elastic easing, finite repeats, and alternating direction. Existing typewriter controls remain available. A Web Animations fallback keeps entrances working if Anime cannot load. **Particle background · this page** uses self-hosted particles.js 2.0.0, with shape, color, count, size, speed, opacity, optional connecting lines, pointer repulsion, and whole-page / edge-only placement. Particle count is capped at 45 on phones. Editing stays still; website preview shows effects. Reduced-motion and the page animation toggle are respected. Both MIT licenses ship at /vendor/anime-LICENSE.txt and /vendor/particles-LICENSE.txt.

This release requires editor version 7 when saving, so an old open editor cannot silently remove newer component data. Export any unsaved draft before refreshing, then import it into the refreshed editor. Publishing the code does not replace saved site content.

### Expanded effects and a simpler editing workspace

The built-in circle-and-triangle **logo** has been removed from the public HTML and editor controls. Existing saved `mark` selections behave as No logo and migrate on the next save. Uploaded and text logos remain available in Pages → Header & page visibility → Logo. Decorative background geometry remains a separate Design setting.

Content now has **Insert**, **Layers**, and **Edit selected** views. Selecting a layer opens its editor, with quick Focus / Animate / Duplicate / Remove actions. The sidebar includes a setting search and Collapse / Expand sections. Canvas tool and icon grid options are under **Canvas options**, leaving page, preview size, and zoom readily available. Logo, header bar/navigation, and page heading controls are grouped separately.

Animation controls are organized into expandable sections:

- **Entrance:** fade, rise/fall, left/right slide, zoom in/out, rotation, drop, horizontal/vertical flip, blur, wipe, roll, skew, and horizontal expansion. Set duration, delay, page stagger, scroll/load/hover/click trigger, and replay on re-entry.
- **Text:** characters or words; typewriter, fade, rise, wave, zoom, rotation, blur, and Anime.js text scrambling; first/last/center/random stagger; timing, cursor, and scramble character sets.
- **Easing & playback:** smooth, linear, bounce, elastic, spring, stepped and custom Bezier easing, plus in/out/in-out variants of Quad, Cubic, Quart, Quint, Sine, Expo, Circ, Back, Bounce, and Elastic. Adjust repeats, continuous looping, alternate/reverse playback, playback speed, spring parameters, and curve coordinates.
- **Transform:** travel distance, rotation angle, starting scale, blur, and transform origin.
- **Hover & ambient motion:** lift, scale, tilt, wiggle, glow; continuous float, pulse, spin, sway, or drift. Hover effects also respond to keyboard focus; ambient motion pauses offscreen and when the document is hidden.
- **Particles:** presets, screen/edge/top/bottom placement; circle/square/triangle/star/polygon/image shapes; custom particle images, outline, multiple colors, count/density, size/opacity randomness, movement direction and boundaries, attraction/collisions, connecting lines, grab/bubble/repulse pointer behavior, push/remove/bubble/repulse clicks, twinkle and size breathing, synchronization, and high-resolution rendering.

Use a preset to start, then expand only the groups you need. **Preview animations** runs effects outside editing mode. Reduced-motion settings and the page animation switch are respected. Particles remain capped at 150 on desktop and 45 on small screens, including after clicks and asynchronous image initialization. This is an expanded preset-and-parameter editor; arbitrary scripts and a keyframe timeline are not exposed.

Saving now requires **editor version 8** to protect new fields from an older open editor. Download any unsaved draft backup before refreshing, then import it in the new editor. Saved website content and uploads remain in D1.

### Page animation timing inheritance fix

The page's **Play when → Page loads** setting now applies to layers whose timing follows the page, including older saved layers that carried an automatic scroll default. A layer's **Play when → Use page timing** makes this explicit. Choosing a different trigger on that layer creates a deliberate override. Existing layer delays and visual effect settings are preserved. **Apply page timing to every layer** clears just the current page's layer trigger overrides.

Returning to Animation keeps the page/layer scope you were editing. The selected layer's quick Animate action still opens that layer. Changing an effect preset preserves your trigger choice. Saving requires editor version 9 to protect the new timing-inheritance field. Download any unsaved draft before refreshing and import it afterward.

### Smooth hover returns and page sections

**Animation → Hover & ambient motion** now includes **Hover easing** and **Return duration**. Leaving a card reverses the current hover animation smoothly; fast pointer re-entry resumes from its current position. Keyboard focus keeps the effect active until focus leaves. Completed effects do not restart just because a hovered card gains focus. Reduced motion and the page animation switch are still respected.

**Content → Insert → + Section** creates an ordered page area. Your current arrangement is preserved as the first section when you add sections to an existing page. **Content → Sections** lets you edit each section's heading, introduction, background color or image/GIF, text color, transparent fill, padding, spacing, minimum height, column count, and grid/single-column/canvas layout. Use the up/down buttons to reorder sections. Removing a section moves its content into another section instead of deleting it.

Choose **Add new content to** in Insert to place new cards, text, media, and lists into a section. Existing content can move between sections from the section's content controls or **Edit selected → Position & size → Section**. Dragging a member converts just its section to canvas layout and leaves other sections alone. Duplicate page copies sections and remaps their content. Section order and content stack on small screens. Up to 30 sections are allowed per page.

Saving requires editor version 10 so an older open editor cannot discard section membership or hover settings. Download any unsaved draft before refreshing, then import it into the updated editor.

### Full-width section bands

Sections now extend across the entire browser width. Their colors and background images reach both edges, sections meet without a gap, and rounded panel edges are removed. The page introduction and header remain centered. This behavior also applies to sections already saved on the site.

In **Content → Sections**, **Content width** chooses between the site's centered content width and using the full section width. The section background is full-width with either choice. **Minimum height only** sets a lower bound; grid and single-column sections grow with their content. Canvas sections also grow to include long text or other content that exceeds a saved frame, and remeasure after content or font sizes change. Small screens continue to stack section contents.

Saving requires editor version 11 to preserve the content-width setting. Export an unsaved draft before refreshing and import it afterward.


### Section-first editor and effect libraries

The editor has a new **outline** on the left: page buttons, then each section with its content in order. Click a row to select it, drag content between sections, drag ⠿ to reorder sections, and use **+** on a section to add a card, text, picture, carousel, video, audio, list, embed, download, or code block directly into it. **Outline** above the canvas shows or hides it; on phones and tablets it opens as a drawer.

**Search actions** (Ctrl+K or ⌘K, or /) finds any action, page, layer, tab, animation preset, or page effect by typing. Shortcuts: Ctrl+S publishes, Ctrl+D duplicates the selected layer, Delete removes it (with a confirmation; undo restores it), and ? lists every shortcut. **File → Take the editor tour** walks through the workspace. Messages appear as notifications in the corner, and **Publish changes** shows a dot while there are unpublished edits.

The editor keeps an on-device safety copy of unpublished edits. If the editor is closed or refreshed before publishing, the next visit offers **Restore draft**, **Download it**, or **Discard**. Nothing is restored or published automatically, and the copy is cleared after a successful publish.

**Animation** now has a clickable preset gallery with hover previews, new entrance effects (pop, swing, drop, unfold, glitch), 3D tilt and tilt-with-shine hover effects (vanilla-tilt), click bursts — confetti, stars, fireworks, side cannons, snow (canvas-confetti) — and hand-drawn highlights, underlines, boxes, circles, and brackets (Rough Notation). **Page effects** add WebGL animated backgrounds (Vanta.js with three.js: waves, fog, net, birds, clouds, globe, cells, dots, rings, halo), smooth scrolling (Lenis), a reading progress bar, and cursor glow, trail, or spotlight. Outline dragging uses SortableJS and the tour uses Driver.js.

All libraries are MIT/ISC licensed, stored under `/vendor` with their licenses, and loaded only on pages that use them. Effects stay off while editing, play in Preview and on the website, and are skipped for visitors who prefer reduced motion. Existing pages keep every effect off until one is chosen.

Saving requires editor version 12. Export an unsaved draft before refreshing an open editor and import it afterward.


### Studio Night editor theme and drag/animation fixes

The editor has a new look: a near-black workspace with one accent color (lime by default; **File → Accent** switches to orange, teal, or violet), Space Grotesk headings with Inter text, and a light variant from the theme button. An **icon rail** on the left opens Outline, Add, Layers, Pages, Design, Motion, and Settings. Selecting an item shows its name at the top of the properties panel with **Edit / Arrange / Animate**: Arrange shows position, size, and section; Animate opens its animation settings. A chip next to Publish counts unpublished changes, and the status bar shows where you are, the zoom level, and whether the on-device safety copy is saved. The selection outline on the canvas uses the accent color. On phones the rail is replaced by the tab row and properties open as a bottom sheet.

Fixes: cards now snap to the grid by their final position, so off-grid cards line up again after a move or resize; resizing near the right edge can no longer push a card past the page; dragging near the edge of a zoomed-in canvas scrolls it; and editing in Preview no longer replays every entrance or rebuilds animated backgrounds and particles on each change. Entrances replay when you switch to Preview or change an animation setting.

No content format changes: saving still uses editor version 12, so an editor that is already open keeps working and shows the new look after a refresh.


### Background creator, more animation libraries, and smoother dragging

**Background creator** (Backdrop in the left rail, the Motion or Design panel, or Ctrl+K → “background”) builds a page background from up to six stacked layers with a live preview: animated gradients (Granim.js), aurora glows, particles (tsParticles: stars, snow, links, bubbles, fireflies, confetti, sparkles, hexagons, rain, triangles, embers), 3D scenes (Vanta.js), floating 3D shapes (Zdog), a neon grid, your own picture with parallax, film grain, and a vignette. Each layer has its own opacity, blend mode, colors, speed, and size. Twelve ready-made looks are a starting point. Nothing changes on the page until **Use this background**; publishing then saves it. The canvas shows a still version while editing; Preview and the website show it moving, and visitors who prefer reduced motion get the still version.

**More animations:** 40 classic entrances and 12 looping attention effects from Animate.css (Jack in the box, bounce, light speed, heartbeat, rubber band, and more), and Typed.js “type and retype each line” for headings. All are under Animation for the page or any selected item.

**Dragging:** the editor no longer re-fits the zoom while a card is being dragged (the page used to shrink under the pointer), the dragged card stays on top, one click selects once, and smart guides snap cards to the edges and centers of nearby cards and the page while showing the position. Hold Alt to move freely without any snapping.

All new libraries are MIT licensed and stored under `/vendor`. Saving still uses editor version 12.


### Bug fixes and a calmer workspace

Keyboard: shortcuts now work after clicking the canvas (Ctrl+S publishes instead of opening the browser's Save dialog; Delete, Ctrl+D, Ctrl+K, and ? work too). Arrow keys nudge the selected item by 10 (Shift: 50, Alt: 1) on canvas layouts, and Escape or the × in the properties header deselects (the × did nothing before).

Selection: choosing an item in the outline, layer list, or search scrolls the canvas to it when it is off-screen, without changing the zoom. The Animate tab follows the newly selected item instead of showing the previous one. The item header no longer appears on page-level panels (Pages, Design, Settings). The Preview size menu says **Custom zoom** after zooming in, instead of still claiming *Whole page*.

Layout: the canvas controls fit on one row; duplicate controls were removed (the page menu while the outline is open, the theme and hide-toolbar buttons in the top bar, the second Animate button, and the extra content tab row on wide screens). With nothing selected, the right panel shows Add instead of repeating the outline's layer list. A **Sections** button joins the rail. On/off settings are switches, color pickers sit beside their labels, and the phone layout uses one row for the top bar and one for the canvas controls.


### Background particles fixed, lighter on the graphics card

Particle layers in the background creator now draw: tsParticles 3 needs its full feature set registered before use, and without it the particles existed but were invisible. Background layers also do much less work per frame: particles run at 30 fps at screen resolution without blurred shadows, 3D (Vanta) scenes render at 1x even on high-DPI displays, the animated gradient draws on a quarter-size canvas, the aurora uses soft gradients instead of large blur filters, the neon grid moves with a GPU-friendly transform, film grain no longer animates, and floating shapes run at 30 fps. The creator labels each layer's graphics load (light, medium, or heavy) and warns before adding a second 3D layer.


While a page has a background-creator background turned on, the built-in paint texture and the outlined circles and triangles are hidden so they do not sit on top of it. Their Design settings are kept, and turning the background off (or hiding all its layers) brings them back.


### Building blocks, Add gallery, and on-canvas editing

Cards are no longer the only way to build a page. The **Add** panel is now a searchable gallery of everything you can place: headings, text, buttons, pictures, shapes, dividers, cards, and sections (Basics); photo galleries, slideshows, video, audio, Lottie animations, embeds, and downloads (Media); stats, skill bars, timelines, quotes, FAQs, tags, lists, and code (Content); and countdowns, scrolling text, and a physics toy (Fun). New items appear in the selected section and scroll into view without changing the zoom.

The new blocks come from one schema (`public/blocks.js`) that drives the server validation, the settings form, and the defaults. Settings use sliders for numbers, segmented buttons for short choices, switches for on/off, and list editors with add, reorder, and remove. Button links accept https://, mailto:, or a page path such as /p/projects.

On the canvas, the selected item shows a quick-action bar (Edit, Animate, Duplicate, bring forward, send backward, Delete), right-click selects an item, and text in blocks (button labels, quotes, stat and skill labels, timeline entries, FAQ questions, tags, countdown headings) can be double-clicked and typed in place.

New libraries, all MIT licensed and loaded only where used: lottie-web (Lottie animations from an uploaded .json), CountUp.js (stats), GLightbox (full-screen gallery viewer), Vivus (shapes and dividers that draw themselves in), Matter.js (the physics toy), and mo.js (burst, sparkle, and ripple click effects under Animation → Click). Blocks show still versions while editing and respect reduced motion.


### New editor layout (replaces the rail, tab rows, and status bar described above)

The editor was rebuilt around three areas, in the style of Framer or Webflow:

- **Top bar:** the menu (backup, import, tour, shortcuts, theme, accent, focus mode, sign out), the left-panel toggle, the page name and save status, desktop/phone width, zoom, search (Ctrl+K), undo/redo, Preview, website preview, the unpublished-change count, and Publish.
- **Left panel:** **Layers** (the page outline; drag items between sections), **Add** (the searchable block gallery and which section new items go into), and **Pages** (switch, add, duplicate, or add a ranked-list page).
- **Properties panel:** changes with the selection. With an item selected it shows the item header (duplicate, delete, deselect), exact X/Y/W/H and section, the item's content and style, and its animation. A selected section shows the section settings. With nothing selected it shows the page, design, page animation and effects, and site settings. Groups collapse and remember it, and the search box filters every visible group.

On the canvas, the selected item has resize grips on all four sides and corners (edges snap to neighbours), and a card's title and text can be double-clicked to edit in place. On phones the canvas comes first and Layers and Properties open as drawers. The editor's look lives in one stylesheet, `public/editor.css`; the old `admin.css`, `editor-theme.css`, and `editor-studio.js` were removed.


### Background controls, page menu, and steadier dragging

With nothing selected, the properties panel starts with **Background**: one switch each for the built-in paint texture and outlined shapes, the background creator (with Edit), classic particles, and any 3D or cursor effect, plus **Make it plain** to turn them all off. Turning paint back on restores its previous strength. A creator background now also hides the second paint layer that used to show through.

Click the page name in the top bar to switch pages or add, duplicate, or open page settings; **+ Add** next to it opens the block gallery.

Dragging follows the pointer exactly, snaps to a neighbouring edge only when close and holds that snap until the pointer moves away (so it no longer flickers between nearby lines), and no longer re-attaches every card at the start of a drag.

## Scroll scenes, item style, dark only

- The site is dark only; light-mode controls were removed (old saved light colours are kept but unused).
- Animation panel → "Scroll animation": parallax, fade, rise, slide, zoom, spin, blur, wipes, circle reveal, words, or custom; start/finish points, direction, easing.
- Section panel → "Pin while scrolling": the section holds on screen for the set scroll length so its items' scroll animations play in order.
- Item properties → "Style": opacity, rotate, corners, blur, shadow, blend.

## Home page

The home page is built in code (public/landing.js, landing.css, motion.js) from saved content only. It scrolls normally: hero, Work (projects), Story (if a Home card titled "About" has blocks), Proportion (a pinned golden-spiral construction), Contact. The WebGL aurora sky (night-sky.js) sits fixed behind every section. Motion uses GSAP 3.13, ScrollTrigger, SplitText and Lenis (public/vendor). In the editor, clicking Work, Story or Contact on the Home canvas selects the matching card.
