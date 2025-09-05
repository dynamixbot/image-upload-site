# Image Upload Site

A simple, fast, and clean image uploading website similar to CubeUpload, built with modern web technologies.

## Tech Stack

- **Frontend Framework**: [Astro](https://astro.build/) - Static site generator with component islands
- **UI Components**: [Svelte](https://svelte.dev/) - Reactive component framework
- **Styling**: [Tailwind CSS](https://tailwindcss.com/) - Utility-first CSS framework
- **Backend/Storage**: [Supabase](https://supabase.com/) - Backend-as-a-Service with file storage

## Features

- 📸 **Drag & Drop Upload** - Intuitive file uploading with drag and drop support
- 🚀 **Fast & Responsive** - Built with modern technologies for optimal performance
- 📱 **Mobile Friendly** - Responsive design that works on all devices
- 🔗 **Direct Links** - Get direct links to your uploaded images instantly
- 📋 **Copy to Clipboard** - One-click URL copying
- 🎨 **Clean UI** - Minimalist design inspired by CubeUpload
- 🔒 **Secure** - Images stored securely with Supabase Storage

## Getting Started

### Prerequisites

- Node.js (version 18 or higher)
- A Supabase account and project

### Installation

1. **Set up Supabase**
   
   a. Create a new project at [supabase.com](https://supabase.com)
   
   b. Go to Storage and create a new bucket called `images`
   
   c. Make the bucket public by updating its settings:
   ```sql
   -- In your Supabase SQL editor, run:
   INSERT INTO storage.buckets (id, name, public)
   VALUES ('images', 'images', true);
   ```
   
   d. Set up storage policies (in Supabase Dashboard > Storage > Policies):
   ```sql
   -- Allow public uploads
   CREATE POLICY "Allow public uploads" ON storage.objects
   FOR INSERT WITH CHECK (bucket_id = 'images');
   
   -- Allow public access
   CREATE POLICY "Allow public access" ON storage.objects
   FOR SELECT USING (bucket_id = 'images');
   ```

2. **Configure environment variables**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` and add your Supabase credentials:
   ```env
   PUBLIC_SUPABASE_URL=your_supabase_project_url
   PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   ```
   
   You can find these in your Supabase project settings under "API".

3. **Install dependencies**
   ```bash
   npm install
   ```

4. **Run the development server**
   ```bash
   npm run dev
   ```
   
   Visit `http://localhost:4321` to see your image upload site!

### Building for Production

```bash
npm run build
```

The built site will be in the `dist/` directory, ready for deployment.

## Deployment

This site can be deployed to any static hosting provider:

- **Vercel**: Connect your GitHub repository for automatic deployments
- **Netlify**: Drag and drop the `dist` folder or connect via Git
- **GitHub Pages**: Use GitHub Actions to build and deploy
- **Cloudflare Pages**: Connect your repository for edge deployment

### Environment Variables for Production

Make sure to set your environment variables in your hosting provider's dashboard:
- `PUBLIC_SUPABASE_URL`
- `PUBLIC_SUPABASE_ANON_KEY`

## 🚀 Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |

Built with ❤️ using Astro, Svelte, Tailwind CSS, and Supabase.
