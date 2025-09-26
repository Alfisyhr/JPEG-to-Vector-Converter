<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Konverter JPEG ke Vector (SVG)</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Icon Library: Lucide -->
    <script src="https://unpkg.com/lucide-react@0.292.0/dist/lucide-react.js"></script>
    <!-- Core Vectorization Library -->
    <script src="https://cdn.jsdelivr.net/npm/imagetracerjs@1.2.6/imagetracer_v1.2.6.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Custom styles for a smoother experience */
        .fade-in {
            animation: fadeIn 0.5s ease-in-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .preview-grid {
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        }
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top: 4px solid #fff;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-900 text-white antialiased">
    <div id="app" class="min-h-screen flex flex-col items-center justify-center p-4 md:p-8">
        
        <!-- Header -->
        <header class="text-center mb-8 md:mb-12">
            <div class="inline-flex items-center justify-center bg-indigo-500/10 p-3 rounded-xl mb-4">
                 <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-indigo-400"><path d="m21.12 5.5-3.03 3.03"/><path d="m6.18 18.81 3.03-3.03"/><path d="M4 4v2.83"/><path d="M4 11.17V14"/><path d="M4 17.17V20"/><path d="M8.83 4H12"/><path d="M15.17 4H18"/><path d="M20 8.83V12"/><path d="M20 15.17V18"/><path d="m18.81 6.18-3.03-3.03"/><path d="m5.5 21.12 3.03-3.03"/><circle cx="12" cy="12" r="10"/></svg>
            </div>
            <h1 class="text-3xl md:text-5xl font-bold tracking-tight bg-gradient-to-br from-white to-gray-400 text-transparent bg-clip-text">Konverter JPEG ke Vector</h1>
            <p class="text-gray-400 mt-2 md:text-lg max-w-2xl mx-auto">Unggah gambar JPEG Anda untuk mengubahnya menjadi grafik vector (SVG) yang skalabel secara instan.</p>
        </header>

        <!-- Main Converter UI -->
        <main class="w-full max-w-6xl bg-gray-800/50 backdrop-blur-xl border border-gray-700 rounded-2xl shadow-2xl p-6 md:p-8 fade-in">
            
            <!-- Upload Area -->
            <div id="upload-container" class="text-center border-2 border-dashed border-gray-600 hover:border-indigo-400 transition-colors duration-300 rounded-xl p-8 md:p-12 cursor-pointer">
                <input type="file" id="file-input" class="hidden" accept="image/jpeg, image/png, image/jpg">
                <div class="flex flex-col items-center justify-center space-y-4 text-gray-400">
                    <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" class="text-gray-500"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" x2="12" y1="3" y2="15"/></svg>
                    <p class="font-semibold">Seret & lepas file gambar Anda di sini</p>
                    <p class="text-sm">atau</p>
                    <button id="browse-btn" class="bg-indigo-600 hover:bg-indigo-500 text-white font-semibold py-2 px-5 rounded-lg transition-all duration-300 shadow-lg shadow-indigo-600/30">
                        Pilih File
                    </button>
                    <p class="text-xs text-gray-500 mt-2">Mendukung: JPEG, JPG, PNG</p>
                </div>
            </div>

            <!-- Previews and Controls -->
            <div id="result-container" class="hidden mt-8">
                <div class="grid gap-6 md:gap-8 preview-grid">
                    <!-- Original Image Preview -->
                    <div class="bg-gray-900/50 p-4 rounded-xl border border-gray-700">
                        <h3 class="text-lg font-semibold mb-3 text-center text-gray-300">Gambar Asli</h3>
                        <div class="aspect-video bg-gray-800 rounded-lg flex items-center justify-center overflow-hidden">
                            <img id="original-preview" src="" alt="Original Preview" class="max-w-full max-h-full object-contain">
                        </div>
                        <p id="image-info" class="text-xs text-gray-500 text-center mt-2"></p>
                    </div>

                    <!-- Vectorized Image Preview -->
                    <div class="bg-gray-900/50 p-4 rounded-xl border border-gray-700">
                        <h3 class="text-lg font-semibold mb-3 text-center text-gray-300">Hasil Vector (SVG)</h3>
                        <div id="vector-preview-container" class="aspect-video bg-gray-800 rounded-lg flex items-center justify-center overflow-hidden">
                           <div id="vector-placeholder" class="text-center text-gray-500">
                                <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><rect width="18" height="18" x="3" y="3" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><path d="M21 15l-5-5L5 21"/></svg>
                               <p class="mt-2 text-sm">Pratinjau vector akan muncul di sini</p>
                           </div>
                           <div id="vector-preview" class="w-full h-full"></div>
                        </div>
                    </div>
                </div>

                <!-- Controls and Actions -->
                <div class="mt-8 bg-gray-900/50 p-6 rounded-xl border border-gray-700">
                    <h3 class="text-xl font-semibold mb-4 text-center">Pengaturan & Aksi</h3>
                    <div class="flex flex-col md:flex-row items-center justify-center gap-6">
                        <button id="convert-btn" disabled class="w-full md:w-auto flex items-center justify-center gap-2 bg-green-600 hover:bg-green-500 text-white font-bold py-3 px-8 rounded-lg transition-all duration-300 shadow-lg shadow-green-600/30 disabled:bg-gray-600 disabled:cursor-not-allowed disabled:shadow-none">
                            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 0 1-9 9H3"/><path d="M3 12a9 9 0 0 1 9-9h9"/><path d="m16 16 5-5-5-5"/><path d="m8 8-5 5 5 5"/></svg>
                            Konversi ke Vector
                        </button>
                        <button id="download-btn" disabled class="w-full md:w-auto flex items-center justify-center gap-2 bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-3 px-8 rounded-lg transition-all duration-300 shadow-lg shadow-indigo-600/30 disabled:bg-gray-600 disabled:cursor-not-allowed disabled:shadow-none">
                             <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" x2="12" y1="15" y2="3"/></svg>
                            Unduh SVG
                        </button>
                        <button id="reset-btn" class="w-full md:w-auto flex items-center justify-center gap-2 bg-red-600 hover:bg-red-500 text-white font-bold py-3 px-8 rounded-lg transition-all duration-300 shadow-lg shadow-red-600/30">
                           <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="1 4 1 10 7 10"/><path d="M3.51 15a9 9 0 1 0 2.13-9.36L1 10"/></svg>
                            Mulai Lagi
                        </button>
                    </div>
                     <div id="status-text" class="text-center text-indigo-300 mt-4 h-5"></div>
                </div>

            </div>
        </main>
        
        <!-- Footer -->
        <footer class="text-center mt-8 md:mt-12 text-gray-500 text-sm">
            <p>Dibuat dengan Gemini & imagetracerjs</p>
        </footer>

    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Element references
            const uploadContainer = document.getElementById('upload-container');
            const fileInput = document.getElementById('file-input');
            const browseBtn = document.getElementById('browse-btn');
            const resultContainer = document.getElementById('result-container');
            const originalPreview = document.getElementById('original-preview');
            const imageInfo = document.getElementById('image-info');
            const vectorPreview = document.getElementById('vector-preview');
            const vectorPlaceholder = document.getElementById('vector-placeholder');
            const convertBtn = document.getElementById('convert-btn');
            const downloadBtn = document.getElementById('download-btn');
            const resetBtn = document.getElementById('reset-btn');
            const statusText = document.getElementById('status-text');

            let originalImageDataUrl = null;
            let svgContent = null;
            let currentFileName = 'vector-image.svg';

            // --- Event Listeners ---

            // Trigger file input click when browse button is clicked
            browseBtn.addEventListener('click', () => fileInput.click());

            // Handle drag and drop
            uploadContainer.addEventListener('dragover', (e) => {
                e.preventDefault();
                uploadContainer.classList.add('border-indigo-400', 'bg-gray-800');
            });
            uploadContainer.addEventListener('dragleave', () => {
                uploadContainer.classList.remove('border-indigo-400', 'bg-gray-800');
            });
            uploadContainer.addEventListener('drop', (e) => {
                e.preventDefault();
                uploadContainer.classList.remove('border-indigo-400', 'bg-gray-800');
                const files = e.dataTransfer.files;
                if (files.length > 0) {
                    handleFile(files[0]);
                }
            });
            
            // Handle file selection from input
            fileInput.addEventListener('change', (e) => {
                if (e.target.files.length > 0) {
                    handleFile(e.target.files[0]);
                }
            });
            
            // Convert button click
            convertBtn.addEventListener('click', () => {
                if (originalImageDataUrl) {
                    traceImage();
                }
            });
            
            // Download button click
            downloadBtn.addEventListener('click', () => {
                if (svgContent) {
                    downloadSVG();
                }
            });
            
            // Reset button click
            resetBtn.addEventListener('click', resetApp);


            // --- Core Functions ---

            function handleFile(file) {
                if (!file.type.startsWith('image/')) {
                    alert('Harap pilih file gambar (JPEG, PNG).');
                    return;
                }
                
                // Save filename for download
                currentFileName = file.name.split('.').slice(0, -1).join('.') + '.svg';

                const reader = new FileReader();
                reader.onload = (e) => {
                    originalImageDataUrl = e.target.result;
                    originalPreview.src = originalImageDataUrl;
                    
                    const img = new Image();
                    img.onload = () => {
                        imageInfo.textContent = `${img.width} x ${img.height}px  |  (${(file.size / 1024).toFixed(1)} KB)`;
                    };
                    img.src = originalImageDataUrl;

                    showResultArea();
                };
                reader.readAsDataURL(file);
            }

            function showResultArea() {
                uploadContainer.classList.add('hidden');
                resultContainer.classList.remove('hidden');
                resultContainer.classList.add('fade-in');
                convertBtn.disabled = false;
            }

            function traceImage() {
                setLoadingState(true, 'Mengonversi gambar, mohon tunggu...');
                
                // imagetracerjs options
                const options = {
                    ltres: 1,
                    qtres: 1,
                    pathomit: 8,
                    rightangleenhance: true,
                    // You can add more options here for user control later
                };
                
                // Use a timeout to allow the UI to update before the heavy work starts
                setTimeout(() => {
                    ImageTracer.imageToSVG(
                        originalImageDataUrl,
                        (svgString) => {
                            svgContent = svgString;
                            vectorPreview.innerHTML = svgContent;
                            vectorPlaceholder.classList.add('hidden');
                            downloadBtn.disabled = false;
                            setLoadingState(false, 'Konversi berhasil!');
                        },
                        options
                    );
                }, 50); // Small delay
            }
            
            function downloadSVG() {
                const blob = new Blob([svgContent], { type: 'image/svg+xml' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = currentFileName;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
            }

            function setLoadingState(isLoading, message = '') {
                statusText.textContent = message;
                if (isLoading) {
                    convertBtn.disabled = true;
                    convertBtn.innerHTML = '<div class="spinner"></div><span>Memproses...</span>';
                } else {
                    convertBtn.disabled = false;
                    convertBtn.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 0 1-9 9H3"/><path d="M3 12a9 9 0 0 1 9-9h9"/><path d="m16 16 5-5-5-5"/><path d="m8 8-5 5 5 5"/></svg>Konversi lagi';
                    // Clear message after a few seconds
                    setTimeout(() => {
                        if (statusText.textContent === message) {
                           statusText.textContent = '';
                        }
                    }, 3000);
                }
            }

            function resetApp() {
                originalImageDataUrl = null;
                svgContent = null;
                fileInput.value = ''; // Reset file input
                
                uploadContainer.classList.remove('hidden');
                uploadContainer.classList.add('fade-in');
                resultContainer.classList.add('hidden');
                
                originalPreview.src = '';
                imageInfo.textContent = '';
                vectorPreview.innerHTML = '';
                vectorPlaceholder.classList.remove('hidden');
                
                convertBtn.disabled = true;
                downloadBtn.disabled = true;
                setLoadingState(false);
                convertBtn.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 0 1-9 9H3"/><path d="M3 12a9 9 0 0 1 9-9h9"/><path d="m16 16 5-5-5-5"/><path d="m8 8-5 5 5 5"/></svg>Konversi ke Vector';

            }
        });
    </script>
</body>
</html>
