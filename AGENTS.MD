# ffmpeg-web Repository Documentation for AI Agents

## Repository Overview

**Project Name:** ffmpeg-web  
**Description:** A Web and Native UI for ffmpeg-wasm that converts video, audio, and images using ffmpeg directly from a web browser or desktop application (via Electron).  
**Main Technologies:** Svelte, TypeScript, Vite, FFmpeg, Electron  
**Live Demo:** https://ffmpeg-web.netlify.app/

## Project Structure

### Root Directory
- `src/` - Main source code directory
- `public/` - Static assets
- `electron/` - Electron-specific files for native application
- `dist/` - Build output directory (generated)
- `node_modules/` - Dependencies (generated)

### Source Code Organization (`src/`)

#### Components (`src/lib/`)
- `lib/ItemCards/` - Main UI card components for different features
  - `MainCards/` - Individual feature cards (MediaEncoding, FileMerge, ImageConvert, etc.)
  - `MainPicker.svelte` - Main tab selector for different operations
  - `VideoOutput.svelte` - Video output settings including rotation, FPS, aspect ratio
  - `BitrateSelection.svelte` - Bitrate configuration UI
  - `FileHandler.svelte` - File selection and handling
  - `ConversionStatus.svelte` - Conversion progress display

- `lib/InnerDialog/` - Dialog components
  - `SettingsDialog.svelte` - Application settings
  - `ImageToVideoDialog.svelte` - Image to video conversion settings
  - `ExtraAudioToVideoSettings.svelte` - Audio to video advanced settings

- `lib/UIElements/` - Reusable UI components
  - `Card/` - Card wrapper components
  - `ChipElements/` - Chip selection UI
  - `Switch.svelte` - Toggle switch component
  - `Dialog.svelte` - Modal dialog component
  - `AdaptiveAsset.svelte` - SVG asset loader

#### TypeScript Logic (`src/ts/`)

- `TabOptions/` - Configuration and state management
  - `ConversionOptions.ts` - Main conversion settings object
  - `EncoderInfo.ts` - Video/audio codec information
  - `InputOptions.ts` - Input file options
  - `MergeOptions.ts` - File merge settings
  - `MetadataOptions.ts` - Metadata editing settings
  - `Settings.ts` - Application settings

- `FFmpegUtils/` - FFmpeg integration
  - `FFmpegBuilder.ts` - Builds FFmpeg commands from user options
  - `FFmpegClass.ts` - FFmpeg wrapper class
  - `FFmpegHandleFileName.ts` - File name handling
  - `FFmpegLoadWrite.ts` - File I/O operations

- `CommandBuilderLogic/` - Command generation for different operations
  - `FileLogic.ts` - File conversion logic
  - `MergeLogic.ts` - File merging logic
  - `ImageLogic.ts` - Image conversion logic
  - `ImageToVideoLogic.ts` - Image to video conversion
  - `MetadataLogic.ts` - Metadata editing logic
  - `AudioToVideoLogic.ts` - Audio to video conversion
  - `InputLogic.ts` - Custom input command handling
  - `FileDivider.ts` - File splitting logic

- `Customization/` - UI customization
  - `Themes.ts` - Theme management
  - `Screensaver.ts` - Screensaver settings
  - `BackgroundType.ts` - Background customization

- `Storage/` - Data persistence
  - `IndexedDatabase.ts` - IndexedDB wrapper
  - `UpdateStorage.ts` - Storage update utilities

- Other utilities:
  - `LanguageAdapt.ts` - Internationalization (i18n) support
  - `ImageHandler.ts` - SVG icon definitions
  - `SaveFile.ts` - File download/save logic
  - `Writables.ts` - Svelte stores for reactive state
  - `Migration.ts` - Data migration utilities

#### Interfaces (`src/interfaces/`)
- `ffmpeg.ts` - FFmpeg-related type definitions
- `chip.ts` - Chip component type definitions

## Key Features

### 1. Media Encoding
- Convert video and audio to various formats
- Support for H264, H265, VP9, VP8, Theora video codecs
- Support for MP3, AAC, Vorbis, Opus, FLAC, ALAC, WAV audio codecs
- Customizable bitrate, FPS, resolution settings
- Video rotation (0°, 90°, 180°, 270°)
- Aspect ratio modification
- Video and audio filters

### 2. File Operations
- Merge multiple video/audio files
- Custom FFmpeg commands
- Trim/cut content by timestamps
- Metadata editing

### 3. Image Processing
- Image format conversion
- Image to video conversion with transitions
- Extract album art from media files

### 4. Audio to Video
- Convert music files to videos with album art and metadata display

## Development Workflow

### Build Commands
```bash
npm install          # Install dependencies
npm run dev          # Start development server
npm run build        # Build for production
npm run preview      # Preview production build
npm run electron     # Run Electron version
npm run check        # Type checking with svelte-check
```

### Adding New Features

To add a new preset/feature like "Rotate Video":

1. **Create a new component** in `src/lib/ItemCards/MainCards/`
   - Follow the pattern of existing components (e.g., `FileMerge.svelte`, `ImageConvert.svelte`)
   - Use the standard card layout with `AdaptiveAsset` for icons and `getLang()` for i18n

2. **Register the feature** in `src/lib/ItemCards/MainPicker.svelte`
   - Add a new chip selection item with appropriate ID and display text
   - Add conditional rendering for the new component

3. **Add translations** in `src/ts/LanguageAdapt.ts`
   - Add entries for all user-facing strings
   - Support languages: English (default), Italian (it), Chinese (zh)

4. **Add icon assets** in `src/ts/ImageHandler.ts`
   - Define SVG icons inline for UI consistency
   - Reference them using `AdaptiveAsset` component

5. **Implement command logic** (if needed)
   - Create logic file in `src/ts/CommandBuilderLogic/`
   - Integrate with FFmpegBuilder if custom FFmpeg commands are required

6. **Update options** (if needed)
   - Add new options to `src/ts/TabOptions/ConversionOptions.ts`
   - Ensure proper state management

### Code Style Guidelines

- Use TypeScript for logic files
- Use Svelte for UI components
- Follow existing component structure patterns
- Use `getLang()` for all user-facing text
- Use `AdaptiveAsset` for icons/images
- Maintain consistent indentation (4 spaces)
- Keep components modular and focused

### State Management

The application uses a combination of:
- Svelte stores (in `Writables.ts`) for reactive global state
- Static objects (like `ConversionOptions`) for configuration
- Event dispatchers for component communication

### Internationalization

All user-facing text must be wrapped in `getLang()` function:
```typescript
getLang("English text here")
```

Add translations in `LanguageAdapt.ts`:
```typescript
["English text here", { it: "Italian", zh: "Chinese" }],
```

## Testing

Currently, the project does not have automated tests. Manual testing workflow:
1. Build the project with `npm run build`
2. Test in development mode with `npm run dev`
3. Verify Electron version with `npm run electron`
4. Check for TypeScript errors with `npm run check`

## Deployment

### Web Version
- Deployed to Netlify
- Uses WebAssembly version of FFmpeg
- Service Worker for offline support

### Electron Version
- Uses native FFmpeg (faster than WebAssembly)
- Supports hardware acceleration
- Can be built locally

### Docker Version
- Available via Dockerfile
- Nginx-based deployment
- HTTPS support with custom certificates

## Important Notes

- The project uses FFmpeg WebAssembly for web version (slower)
- Electron version uses native FFmpeg (much faster)
- All media processing happens locally (client-side)
- No data is sent to external servers
- Files are handled in memory or via File System API

## Common Patterns

### Creating a Switch Option
```svelte
<Switch
    text={getLang("Option description")}
    checked={ConversionOptions.someOption}
    on:change={({ detail }) => (ConversionOptions.someOption = detail)}
></Switch>
```

### Creating a Chip Selection
```svelte
<ChipContainer>
    <Chip
        selectionItems={[
            { display: getLang("Option 1"), id: "opt1", selected: true },
            { display: getLang("Option 2"), id: "opt2" },
        ]}
        on:userSelection={({ detail }) => {
            // Handle selection
        }}
    ></Chip>
</ChipContainer>
```

### Using AdaptiveAsset for Icons
```svelte
<AdaptiveAsset asset="iconName" width={26}></AdaptiveAsset>
```

## Dependencies

### Runtime Dependencies
- `@ffmpeg/ffmpeg` - FFmpeg WebAssembly builds (v0.11 and v0.12)
- `@zip.js/zip.js` - ZIP file creation
- `electron` - Desktop application framework
- `jszip` - ZIP handling
- `context-filter-polyfill` - Web compatibility

### Development Dependencies
- `svelte` - UI framework
- `vite` - Build tool
- `typescript` - Type checking
- `@sveltejs/vite-plugin-svelte` - Svelte integration
- `vite-plugin-cross-origin-isolation` - COOP/COEP headers

## File Size Considerations

The build output includes:
- Main bundle: ~355 kB (gzipped: ~107 kB)
- FFmpeg utils: ~173 kB (gzipped: ~63 kB)
- JSZip: ~97 kB (gzipped: ~30 kB)
- Styles: ~8 kB (gzipped: ~2 kB)

Total initial load is approximately 200-250 kB gzipped.

## Browser Compatibility

- Modern browsers with WebAssembly support
- Requires SharedArrayBuffer (COOP/COEP headers)
- Progressive Web App (PWA) installable
- Offline capable via Service Worker

## Contributing Guidelines

When contributing:
1. Maintain consistent code style with existing code
2. Add i18n support for all new user-facing text
3. Test in both web and Electron environments
4. Keep components modular and reusable
5. Document complex logic with comments
6. Follow accessibility best practices (though some warnings exist)

## Known Issues

- Some accessibility warnings (A11y) in Svelte components
- Requires COOP/COEP headers for SharedArrayBuffer
- Microsoft Edge may require disabling "Enhance security" for better performance
