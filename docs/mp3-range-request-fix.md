# MP3 Seeking Fails in Chrome: Range Request Issue

## The Problem

When clicking timestamps to jump to specific points in an audio file, Chrome starts playback at 0:00 instead of the requested time. Firefox may work inconsistently, sometimes requiring multiple clicks to reach the correct position.

## Root Cause

Chrome requires HTTP Range Requests to seek within audio files. This allows the browser to request specific byte ranges (e.g., "give me bytes 50000-51000") rather than downloading the entire file from the beginning.

For range requests to work, the server must:
- Include `Accept-Ranges: bytes` in response headers
- Respond to range requests with `206 Partial Content` status (not `200 OK`)

Some hosting configurations—particularly older Azure Blob Storage setups—don't support range requests properly. Without this support, Chrome cannot seek, and your timestamp links won't work.

## How to Check Your Audio Hosting

Run this command in your terminal, replacing the URL with your audio file:

```bash
curl -I -H "Range: bytes=0-1000" "https://your-server.com/path/to/audio.mp3"
```

**Working response (supports seeking):**
```
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Range: bytes 0-1000/186476716
Content-Length: 1001
```

**Broken response (seeking will fail in Chrome):**
```
HTTP/1.1 200 OK
Content-Length: 186476716
```

If you see `200 OK` with the full file size instead of `206 Partial Content`, range requests aren't working.

## Solutions

### Option 1: Fix the Server Configuration

If using Azure Blob Storage, the issue may be an outdated API version. Re-uploading files through the Azure Portal or Azure Storage Explorer with current settings often resolves this.

### Option 2: Host Audio on GitHub

GitHub Pages fully supports range requests. You can keep your static site on its current host while serving audio from GitHub:

1. Create a repository for audio files (or use an objects branch in your existing repo)
2. Enable GitHub Pages on that repo/branch
3. Update your audio `src` URLs to point to the GitHub Pages URL

**Example:**
```html
<audio id="audio-player" src="https://yourusername.github.io/audio-repo/filename.mp3">
```

### Option 3: Use a CDN

Most CDNs (Cloudflare, AWS CloudFront, etc.) support range requests by default and can sit in front of your existing storage.

## Testing the Fix

After moving files or updating server configuration, run the curl test again and confirm you see `206 Partial Content`. Then test timestamp clicking in Chrome—it should now jump directly to the correct position.

## Verified Solution

Testing confirmed that hosting MP3 files on GitHub works correctly with range requests and timestamp seeking.

# My Testing and Resolution

On January 7, 2026, I conducted a test using one of he oral histories from the "Georgia Dentel Project" by checking a .mp3 interview recording housed in Azure Blob Storage.  The test and its return were:

```
curl -I -H "Range: bytes=0-1000" "https://collectionbuilder.blob.core.windows.net/objs/Georgia_Dentel/dg_1750784116.mp3"

HTTP/1.1 200 OK
Content-Length: 63656432
Content-Type: application/octet-stream
Content-MD5: yMOJROeAtiY9FZA5AANeFw==
Last-Modified: Sat, 06 Dec 2025 05:28:05 GMT
ETag: 0x8DE34883BC4077D
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: c254744d-d01e-00a9-711b-80f690000000
x-ms-version: 2009-09-19
x-ms-lease-status: unlocked
x-ms-blob-type: BlockBlob
Date: Wed, 07 Jan 2026 21:18:43 GMT
```

This test demonstrates that my Azure Blob, one that was uploaded recently using `Microsoft Azure Storage Explorer`, does NOT conform to the necessary `partial content` requirement for reliable synchronization and accurate sync.

## Testing Digital Ocean Spaces

To test DO I create a new folder in the library `Spaces` storage at https://cloud.digitalocean.com/spaces/digital-grinnell?path=Georgia-Dentel-AV-Testing%2F.  In that "space" I uploaded a copy of the Kit Wall .mp3 interview audio, namely `dg_1750784116.mp3` with a CDN endpoint of:  

https://digital-grinnell.nyc3.cdn.digitaloceanspaces.com/Georgia-Dentel-AV-Testing/dg_1750784116.mp3

### Repeating the Above curl Test

The command and return...  

```
curl -I -H "Range: bytes=0-1000" "https://digital-grinnell.nyc3.cdn.digitaloceanspaces.com/Georgia-Dentel-AV-Testing/dg_1750784116.mp3"

HTTP/2 206 
date: Wed, 07 Jan 2026 21:38:44 GMT
content-type: audio/mpeg
content-length: 1001
cf-ray: 9ba69a940b14e773-DEN
last-modified: Wed, 07 Jan 2026 21:35:05 GMT
x-rgw-object-type: Normal
etag: "c8c38944e780b6263d15903900035e17"
x-amz-request-id: tx00000ed37f765b5ca394c-00695ed264-1316e6c63-nyc3c
vary: Origin, Access-Control-Request-Headers, Access-Control-Request-Method
strict-transport-security: max-age=15552000; includeSubDomains; preload
x-do-cdn-uuid: d1ab11d8-1193-4adf-ab4a-ca0744409ccb
cache-control: max-age=604800
content-range: bytes 0-1000/63656432
cf-cache-status: MISS
set-cookie: __cf_bm=FaynYbHQui6jMbNlmD_NB8mMuIbpv9V2uQRU4p_Q3Hw-1767821924-1.0.1.1-wm0FgXZLqftjl37o0clyjepDgwE934deZLEF_EFhS_jELwD8TNP.0ate7Ayd.ryWPLiTQmiFp2syTH4rJr44ogMaLU6RYF2Qhrk38CFJTRE; path=/; expires=Wed, 07-Jan-26 22:08:44 GMT; domain=.digitaloceanspaces.com; HttpOnly; Secure; SameSite=None
server: cloudflare
```

This seems to suggest that using DO Spaces in place of Azure Blob Storage might be wise.  

### Next Steps

Now I'm going to replace the current https://collectionbuilder.blob.core.windows.net/objs/Georgia_Dentel/dg_1750784116.mp3 Azure reference in the `~/GitHub/GCCB-Georgia-Dentel-Project` project with the new DigitalOcean Spaces reference, and test the result at https://calm-ocean-061ff161e.1.azurestaticapps.net/items/dg_1750784116.html.  

