# Video Transcript Playback Fix

**Date:** January 13, 2026  
**Issue:** Video (.mp4) files with transcripts were not displaying a playback element, while audio (.mp3) files worked correctly.

## Problem Description

When viewing items with transcripts in the GCCB-project-template (a combination of code from collectionbuilder-csv and oralhistoryasdata), audio files (.mp3) displayed properly with a playback element and transcript, but video files (.mp4) showed nothing - no player at all.

## Investigation

### Code Examined

1. **Current Project (_layouts/item/transcript.html):**
   - This layout handles all transcript-based item displays
   - Contains logic to detect different media types (YouTube, Vimeo, audio, etc.)
   - Sets an `av` variable based on the media type detected in `page.object_location`
   - Includes the appropriate player via: `{% include transcript/player/{{av}}.html %}`

2. **Player Files (_includes/transcript/player/):**
   - Existing files: `mp3.html`, `vimeo.html`, `youtube.html`, `soundcloud.html`, `internetarchive.html`
   - **Missing:** `mp4.html`

3. **Comparison with oralhistoryasdata:**
   - The source project had the same structure
   - Also did not have an explicit mp4 check or mp4.html player
   - Both projects were missing this functionality

### Root Cause

In `_layouts/item/transcript.html` (lines 32-47), the media type detection logic checked for:
- `vimeo` → sets `av = "vimeo"`
- `youtu` → sets `av = "youtube"`
- `mp3` → sets `av = "mp3"`
- `soundcloud` → sets `av = "soundcloud"`
- `archive.org` → sets `av = "internetarchive"`

**But there was no check for `mp4`**, so:
- When a video file was encountered, `av` was never set
- The `{% if av %}` condition on line 64 failed
- No player was included in the output
- Result: blank space where the video player should be

## Solution Implemented

### 1. Created Video Player Component

**File:** `_includes/transcript/player/mp4.html`

```html
<div id="player" class="ratio ratio-16x9">
    <video id="video-player" class="w-100 iframe-player" controls preload="metadata">
        <source src="{{page.object_location}}" type="video/mp4">
        Your browser does not support the video element.
    </video>
</div>
<script>
    var mediaElement = document.getElementById('video-player');
    function timestampMP3(x) {
        mediaElement.currentTime = x;
        mediaElement.play();
    }
</script>
```

**Key Features:**
- Uses HTML5 `<video>` element for mp4 playback
- Maintains 16:9 aspect ratio with Bootstrap's `ratio` class
- Includes `timestampMP3()` function for transcript synchronization
- Provides fallback message for unsupported browsers

### 2. Updated Media Type Detection

**File:** `_layouts/item/transcript.html` (line 40-42)

Added the mp4 detection logic:

```liquid
{%- elsif page.object_location contains 'mp4' -%}
{% assign av = "mp4" %}
```

This was inserted between the mp3 check and the soundcloud check, so the complete logic now flows:
1. vimeo
2. youtube
3. mp3 (audio)
4. **mp4 (video)** ← NEW
5. soundcloud
6. archive.org

## How It Works Now

1. When an item with a transcript is loaded and `object_location` contains `.mp4`:
   - The `av` variable is set to `"mp4"`
   - The template includes `_includes/transcript/player/mp4.html`
   - A video player appears with the mp4 file loaded
   - Clicking timestamps in the transcript seeks to that position in the video

2. The video player integrates seamlessly with:
   - Transcript display and highlighting
   - Timestamp click-to-seek functionality
   - Media scroll wrapper (if enabled)
   - Overall transcript layout

## Testing

After rebuilding the site, video items with transcripts (such as Kit Wall's interview in Georgia-Dentel.csv) should now display:
- Video player with controls
- Synced transcript below
- Working timestamp navigation

## Files Modified

- `_layouts/item/transcript.html` - Added mp4 detection logic
- `_includes/transcript/player/mp4.html` - Created new video player component
