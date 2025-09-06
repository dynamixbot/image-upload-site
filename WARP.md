# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a modern image upload site built with Astro, Svelte, and Supabase. It provides drag-and-drop image uploading functionality with direct URL generation, similar to CubeUpload. The architecture uses Astro for static site generation with Svelte component islands for interactivity.

## Tech Stack Architecture

- **Frontend Framework**: Astro (static site generator with component islands)
- **Interactive Components**: Svelte 5 (reactive components with client-side hydration)
- **Styling**: Tailwind CSS 4 (utility-first CSS via Vite plugin)
- **Backend**: Supabase (storage for images, no database tables used)
- **Build Tool**: Vite (integrated through Astro)

## Development Commands

```bash
# Install dependencies
npm install

# Start development server (localhost:4321)
npm run dev

# Build for production
npm run build

# Preview production build locally
npm run preview

# Run Astro CLI commands
npm run astro [command]
```

## Architecture Overview

### Core Application Flow
1. **Entry Point**: `src/pages/index.astro` - Single-page application entry
2. **Main Component**: `src/components/ImageUpload.svelte` - Handles all upload logic and UI
3. **Supabase Integration**: `src/lib/supabase.js` - Simple client configuration
4. **Styling**: `src/styles/global.css` - Imports Tailwind CSS

### Key Design Patterns
- **Island Architecture**: Svelte components are hydrated client-side using `client:load` directive
- **Progressive Enhancement**: Core functionality works with JavaScript disabled (file selection)
- **No Backend Logic**: All operations use Supabase client-side SDK
- **File Naming**: Timestamp + random string strategy for unique filenames

### Supabase Configuration
The application expects a Supabase project with:
- Storage bucket named `images` (public)
- Two storage policies for public upload and access
- No database tables (storage-only usage)

Required environment variables:
- `PUBLIC_SUPABASE_URL`: Your Supabase project URL
- `PUBLIC_SUPABASE_ANON_KEY`: Supabase anonymous key

### Component Structure
- **Single Main Component**: `ImageUpload.svelte` contains all functionality
- **State Management**: Local component state (no external state management)
- **File Handling**: Direct browser File API usage with Supabase Storage
- **Progress Tracking**: Simple percentage-based upload progress

## Development Environment Setup

1. **Supabase Setup Required**: 
   - Create `images` bucket (public)
   - Configure storage policies for uploads and access
   - Get project URL and anon key

2. **Environment Configuration**:
   ```bash
   cp .env.example .env
   # Edit .env with your Supabase credentials
   ```

3. **VS Code Extensions**:
   - Astro extension is recommended (see `.vscode/extensions.json`)

## Common Development Tasks

### Testing Image Upload Functionality
```bash
# Start dev server and test at localhost:4321
npm run dev
```

### Building for Deployment
```bash
# Build static files to dist/
npm run build

# Test production build locally
npm run preview
```

### Adding New Image Processing Features
- Modify `src/components/ImageUpload.svelte`
- Image validation happens in `uploadFiles()` function
- File processing occurs before Supabase upload

### Styling Changes
- Global styles: `src/styles/global.css`
- Component styles: Use Tailwind classes in Svelte components
- Tailwind configuration: Handled automatically by `@tailwindcss/vite`

## File Upload Architecture

### Upload Flow
1. File selection (drag/drop or file picker)
2. Client-side validation (image types only)
3. Generate unique filename (timestamp + random)
4. Upload to Supabase storage bucket
5. Get public URL and display results

### Error Handling
- File type validation on client-side
- Supabase upload errors logged to console
- Failed uploads are skipped, successful ones continue

### File Storage Strategy
- Files stored in Supabase `images` bucket
- Public access (no authentication required)
- Filename format: `${timestamp}-${random}.${extension}`

## Deployment Considerations

- **Static Site**: Builds to `dist/` directory
- **Environment Variables**: Set `PUBLIC_SUPABASE_URL` and `PUBLIC_SUPABASE_ANON_KEY` in hosting provider
- **Supabase Bucket**: Must be configured as public with appropriate policies
- **No Server Required**: Pure static hosting (Vercel, Netlify, etc.)
