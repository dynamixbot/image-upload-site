<script>
  import { supabase } from '../lib/supabase.js';
  
  let dragOver = false;
  let uploading = false;
  let uploadedImages = [];
  let progress = 0;

  function handleDragOver(e) {
    e.preventDefault();
    dragOver = true;
  }

  function handleDragLeave(e) {
    e.preventDefault();
    dragOver = false;
  }

  function handleDrop(e) {
    e.preventDefault();
    dragOver = false;
    const files = Array.from(e.dataTransfer.files);
    uploadFiles(files);
  }

  function handleFileSelect(e) {
    const files = Array.from(e.target.files);
    uploadFiles(files);
  }

  async function uploadFiles(files) {
    const imageFiles = files.filter(file => file.type.startsWith('image/'));
    
    if (imageFiles.length === 0) {
      alert('Please select image files only');
      return;
    }

    uploading = true;
    
    for (let i = 0; i < imageFiles.length; i++) {
      const file = imageFiles[i];
      progress = Math.round(((i + 1) / imageFiles.length) * 100);
      
      try {
        const fileExt = file.name.split('.').pop();
        const fileName = `${Date.now()}-${Math.random().toString(36).substring(2)}.${fileExt}`;
        
        const { data, error } = await supabase.storage
          .from('images')
          .upload(fileName, file);

        if (error) {
          console.error('Upload error:', error);
          continue;
        }

        const { data: { publicUrl } } = supabase.storage
          .from('images')
          .getPublicUrl(fileName);

        uploadedImages = [...uploadedImages, {
          name: file.name,
          url: publicUrl,
          size: file.size
        }];
        
      } catch (error) {
        console.error('Error uploading file:', error);
      }
    }
    
    uploading = false;
    progress = 0;
  }

  function copyToClipboard(url) {
    navigator.clipboard.writeText(url).then(() => {
      // Could add a toast notification here
      console.log('URL copied to clipboard');
    });
  }

  function formatFileSize(bytes) {
    if (bytes === 0) return '0 Bytes';
    const k = 1024;
    const sizes = ['Bytes', 'KB', 'MB', 'GB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
  }
</script>

<div class="max-w-4xl mx-auto p-6">
  <!-- Header -->
  <div class="text-center mb-8">
    <h1 class="text-4xl font-bold text-gray-800 mb-2">Image Upload</h1>
    <p class="text-gray-600">Simple, fast, and free image hosting</p>
  </div>

  <!-- Upload Area -->
  <div 
    class="border-2 border-dashed rounded-lg p-12 text-center transition-all duration-200 {dragOver ? 'border-blue-500 bg-blue-50' : 'border-gray-300 hover:border-gray-400'}"
    on:dragover={handleDragOver}
    on:dragleave={handleDragLeave}
    on:drop={handleDrop}
  >
    {#if uploading}
      <div class="space-y-4">
        <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-500 mx-auto"></div>
        <p class="text-gray-600">Uploading... {progress}%</p>
        <div class="w-full bg-gray-200 rounded-full h-2">
          <div class="bg-blue-500 h-2 rounded-full transition-all duration-300" style="width: {progress}%"></div>
        </div>
      </div>
    {:else}
      <div class="space-y-4">
        <div class="text-6xl text-gray-400">📷</div>
        <div>
          <p class="text-xl text-gray-600 mb-2">Drag & drop your images here</p>
          <p class="text-gray-500 mb-4">or</p>
          <label class="bg-blue-500 hover:bg-blue-600 text-white px-6 py-3 rounded-lg cursor-pointer inline-block transition-colors">
            Choose Files
            <input 
              type="file" 
              multiple 
              accept="image/*" 
              class="hidden" 
              on:change={handleFileSelect}
            >
          </label>
        </div>
        <p class="text-sm text-gray-500">Supports: JPG, PNG, GIF, WebP</p>
      </div>
    {/if}
  </div>

  <!-- Uploaded Images -->
  {#if uploadedImages.length > 0}
    <div class="mt-12">
      <h2 class="text-2xl font-bold text-gray-800 mb-6">Uploaded Images</h2>
      <div class="space-y-4">
        {#each uploadedImages as image}
          <div class="bg-white border rounded-lg p-4 shadow-sm">
            <div class="flex items-center space-x-4">
              <img src={image.url} alt={image.name} class="w-16 h-16 object-cover rounded">
              <div class="flex-1">
                <p class="font-medium text-gray-800">{image.name}</p>
                <p class="text-sm text-gray-500">{formatFileSize(image.size)}</p>
              </div>
              <div class="flex space-x-2">
                <button 
                  class="bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded text-sm transition-colors"
                  on:click={() => copyToClipboard(image.url)}
                >
                  Copy URL
                </button>
                <a 
                  href={image.url} 
                  target="_blank" 
                  class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded text-sm transition-colors"
                >
                  View
                </a>
              </div>
            </div>
            <div class="mt-3 p-2 bg-gray-50 rounded text-sm font-mono text-gray-600 break-all">
              {image.url}
            </div>
          </div>
        {/each}
      </div>
    </div>
  {/if}
</div>
