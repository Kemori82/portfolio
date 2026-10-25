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

This release requires editor version 4 when publishing, to protect the new content fields from older open editor tabs. Export any unsaved draft using **File → Download backup**, refresh the admin page, then import it if needed.


### Zoom, shortcuts, and rich media

Hold **Ctrl** and scroll over the canvas (including directly over a card) to zoom in or out around the pointer. Ordinary scrolling still pans the page. The toolbar also has **− / + / Fit** controls and a zoom percentage. Choosing a preview size resets the custom zoom. **Ctrl+Z** undoes and **Ctrl+Y** or **Ctrl+Shift+Z** redoes edits, including while the canvas is focused. Uploads and publishing finish before history can change.

The picture pickers now accept animated GIFs up to 900 KB and preserve their frames. Other pictures continue to resize automatically. GIFs can be used for card pictures, popup pictures, icons, and the background. Favicon uploads still become a small PNG.

**Cards → Insert content → Audio / music, Video, or Embed** adds a section to the selected card's popup. Audio/video uploads support MP3, WAV, FLAC, Ogg, M4A, MP4, and WebM, up to 20 MB each, with playback controls and seeking. Formats/codecs still depend on the visitor's browser. Larger media can use a direct HTTPS media-file URL. Videos can have an optional poster picture. Playback is user initiated and stops when the popup or full website preview closes.

For embeds, paste a YouTube, Vimeo, Spotify, or SoundCloud URL or the provider's iframe embed code. Only the iframe URL is used; arbitrary scripts and HTML are not run. Embed height is adjustable. Availability depends on the provider, the content owner's embed settings, and the visitor's browser.

All uploads share the existing 50 MB library. Audio/video is split into small database rows and served with HTTP byte ranges to support seeking within the free Cloudflare setup. No additional hosting service or paid plan is required by this update.
