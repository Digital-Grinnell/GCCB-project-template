# Merge Strategy - January 2026

**Branch:** Jan26  
**Date:** January 13, 2026  
**Purpose:** Bring recent improvements from upstream projects into GCCB-project-template

## Source Projects Analysis

### 1. CollectionBuilder-CSV (../collectionbuilder-csv)

**Recent Key Changes Since Dec 2024:**

#### High Priority - Transcript Related
- **Interview Date Display** (748b2df) - Added interview_date display to transcript-metadata.html
- **Transcript Support for Compound Objects** (1c174ae) - Adds transcript support to compound_object parent
- **Media Modal Improvements** (b46177d, eb91d6d, 2bac0b1, 1679eef, de040fe):
  - New audio and video modal embeds
  - Cleaned up video modal options
  - Tested modal includes
- **Video Player Enhancements** (3542113, d76fc74):
  - Added transcript and cover option to video include
  - Cover image support for video player

#### Medium Priority - UI/UX Improvements
- **Asset Location Updates** (c8432bd, 6f0ea99, c6d18e3) - Cleaned up asset locations, custom lib paths
- **About Page Improvements** (515a57f, 4db4404, multiple commits) - TOC improvements, layout fixes
- **Advanced Search & Browse** (48cd0fc, 2d40828, 65c1ee6, etc.) - Faceted search, advanced search modal
- **Map Auto-Center** (b16eaf9, a997ca7, a1b6e5b, 0c7e68c) - Auto-fit map to features
- **Back to Top** (93a72fd, 48c0b4c) - More accessible anchor-based implementation

#### Lower Priority - Maintenance
- **Footer Date Format** (57c39a2) - Added time to "Last updated" in footer
- **CB Icons Fix** (15f5d9f) - Eliminated cb-icons.svg error
- **Ruby/Gem Updates** (various) - Updated dependencies, fixed deprecation warnings
- **Documentation** (546440a, efdeabc) - Added WORKFLOW.md

### 2. OralHistoryAsData (../oralhistoryasdata)

**Recent Key Changes Since Dec 2024:**

#### Critical - Bug Fixes
- **Timestamp Chrome Issue Fix** (c05cf04) - Simplified timestampMP3() function to fix Chrome playback issues
  - **ALREADY IN THIS PROJECT** via commit 590b4dd

#### High Priority - OHD Features
- **Bio Button & Markdown Bios** (0c04e84) - Added bio button and logic for markdown biography display
- **PDF Generation** (81dd72c, 9353ce7, edfd39a) - PagedJS output for PDF generation/download
- **Child Objects in Transcript** (d3b62ce) - Display child objects within transcript layout
- **Transcript Section** (feaf10b, 233736b) - Improved transcript section handling

#### Medium Priority - Data/Metadata
- **CSV Browse/About Updates** (a4fd838) - Pulled in new CSV additions for browse and about pages
- **Visualization Page** (10d1222, cb8df2b) - Added visualization page with tooltips
- **Analytics Updates** (a7c3997) - Updated analytics

## Current Project Status

**Last Sync Points:**
- Based on CollectionBuilder-CSV (Digital-Grinnell fork)
- Integrated OralHistoryAsData features around August 2024
- Recent local work (Dec 2024 - Jan 2026):
  - Fixed mp3 playback control (590b4dd)
  - Added mp4 video player for transcripts (just added)
  - Azure deployment configuration
  - Footer timestamp improvements

## Merge Strategy

### Phase 1: Critical Updates (Do First)

#### 1.1 Already Fixed ✓
- ~~Timestamp Chrome issue~~ - Already applied via commit 590b4dd
- ~~Video/mp4 transcript player~~ - Just fixed in current session

#### 1.2 Interview Date Display
**Source:** collectionbuilder-csv (748b2df)
**Files:** `_includes/transcript/item/transcript-metadata.html`
**Action:** 
- Review and merge the interview_date display logic
- Test with Georgia-Dentel dataset

#### 1.3 Transcript Support for Compound Objects
**Source:** collectionbuilder-csv (1c174ae)
**Files:** `_layouts/item/compound_object.html` (or related)
**Action:**
- Review compound object parent transcript support
- May already be in OHD version - verify

### Phase 2: Enhanced Features (High Value)

#### 2.1 Bio Button & Markdown Bios
**Source:** oralhistoryasdata (0c04e84)
**Files:**
- `_includes/transcript/item/bio-modal.html`
- `_includes/transcript/item/download-buttons.html`
- `_includes/transcript/item/transcript-metadata.html`
- `_data/theme.yml`
**Action:**
- Add bio button display logic
- Create markdown bio support
- Update download buttons section
**Impact:** Enhances oral history display

#### 2.2 PDF Generation with PagedJS
**Source:** oralhistoryasdata (81dd72c)
**Files:**
- `_includes/transcript/js/pagedjs-js.html` (new file)
- `_includes/transcript/item/download-buttons.html`
- `_includes/transcript/item/transcript.html`
- `_layouts/item/transcript.html`
**Action:**
- Add PagedJS library include
- Implement PDF generation button
- Test PDF output quality
**Impact:** Major feature - allows transcript PDF downloads

#### 2.3 Child Objects in Transcript Layout
**Source:** oralhistoryasdata (d3b62ce)
**Files:** Multiple transcript-related files
**Action:**
- Review child object display logic
- Integrate with existing compound object handling
**Impact:** Better compound object support

#### 2.4 Media Modal Improvements
**Source:** collectionbuilder-csv (b46177d series)
**Files:** Multiple modal and player includes
**Action:**
- Review modal implementation
- Consider if needed (transcripts use inline players)
- May skip if not applicable
**Impact:** Medium - depends on use case

#### 2.5 Video Player Cover Image
**Source:** collectionbuilder-csv (d76fc74, 3542113)
**Files:** 
- `_includes/item/video-player.html`
- Related video includes
**Action:**
- Add poster/cover image support to video player
- Update our new mp4.html player to support this
**Impact:** Better video presentation

### Phase 3: UI/UX Improvements (Medium Priority)

#### 3.1 Map Auto-Center Feature
**Source:** collectionbuilder-csv (multiple commits)
**Files:** Map-related JavaScript and includes
**Action:**
- Add auto-center-map configuration option
- Update map initialization logic
**Impact:** Better map UX

#### 3.2 Advanced Search & Faceted Browse
**Source:** collectionbuilder-csv (multiple commits)
**Files:** Browse page, search modals, configuration
**Action:**
- Review against current browse functionality
- Consider if advanced search fits use case
- May defer if not needed
**Impact:** Enhanced search capabilities

#### 3.3 About Page TOC Improvements
**Source:** collectionbuilder-csv (multiple commits)
**Files:** About layouts and includes
**Action:**
- Minor styling and padding improvements
- Heading handling
**Impact:** Low - cosmetic

### Phase 4: Maintenance & Infrastructure (As Needed)

#### 4.1 Asset Location Updates
**Source:** collectionbuilder-csv (multiple commits)
**Action:**
- Review custom asset paths
- Update if needed for CDN/external assets
**Impact:** Infrastructure improvement

#### 4.2 Ruby/Gem Updates
**Source:** collectionbuilder-csv (various)
**Action:**
- Update Gemfile
- Fix deprecation warnings (SASS migration, etc.)
**Impact:** Future-proofing

#### 4.3 Documentation Updates
**Source:** collectionbuilder-csv (WORKFLOW.md, etc.)
**Action:**
- Review and adapt WORKFLOW.md
- Update README as needed
**Impact:** Developer experience

## Recommended Merge Order

### Immediate (This Session)
1. ✓ Created Jan26 branch
2. Interview date display (Phase 1.2)
3. Bio button & markdown bios (Phase 2.1)
4. Child objects in transcript (Phase 2.3)

### Next Session
5. PDF generation with PagedJS (Phase 2.2)
6. Video player cover image support (Phase 2.5)
7. Map auto-center (Phase 3.1)

### Future Consideration
8. Advanced search/faceted browse (Phase 3.2)
9. Media modals (Phase 2.4) - if needed
10. Infrastructure updates (Phase 4)

## Files Likely to Have Conflicts

Based on the analysis, these files will need careful merging:

1. `_includes/transcript/item/transcript-metadata.html` - Multiple sources modified
2. `_includes/transcript/item/download-buttons.html` - OHD added bio logic
3. `_includes/transcript/player/mp3.html` - We just modified, OHD fixed
4. `_layouts/item/transcript.html` - We just modified for mp4, OHD added PDF
5. `_data/theme.yml` - Various configuration additions
6. About page layouts - Multiple CB-CSV improvements

## Testing Checklist After Merge

- [ ] Audio (.mp3) playback with transcript
- [ ] Video (.mp4) playback with transcript  
- [ ] Timestamp click-to-seek functionality
- [ ] Interview date display (if present in metadata)
- [ ] Bio button display (if bio data present)
- [ ] PDF generation button and output
- [ ] Child objects in compound items
- [ ] Map display and auto-center
- [ ] Browse and search functionality
- [ ] About page TOC
- [ ] Build without errors (`bundle exec jekyll build`)
- [ ] Site deployment to Azure

## Notes

- We already have the Chrome timestamp fix (590b4dd)
- We already have mp4 video support (just added)
- Our mp3.html already matches the simplified version from OHD
- Need to be careful not to overwrite our recent mp4.html addition
- Test thoroughly with Georgia-Dentel dataset which has both audio and video
