# Offline AI Demo 🤖

A demonstration of offline AI capabilities using SvelteKit and Transformers.js. This application runs machine learning models entirely in your browser without requiring an internet connection (after the initial model download).

## 🎥 Demo

![Offline AI Demo](https://github.com/user-attachments/assets/fb600814-074c-4fdf-ad3c-c1daee150c76)

## Features

- 🚀 **Sentiment Analysis**: Analyze the emotional tone of text (positive/negative)
- 📝 **Text Summarization**: Generate concise summaries of longer texts
- 🔒 **Privacy-First**: All processing happens locally on your device
- 📡 **Fully Offline**: Works without internet after initial model download
- ⚡ **Fast & Lightweight**: Uses optimized ONNX models via WebAssembly
- 🎭 **Demo Mode**: Falls back to mock analysis when models can't be loaded

## Technologies Used

- **SvelteKit 2.0**: Modern web framework for building the UI with TypeScript
- **Transformers.js**: Run Hugging Face models in the browser
- **TailwindCSS**: Utility-first CSS framework for styling
- **ONNX Runtime**: Execute machine learning models via WebAssembly
- **TypeScript**: Type-safe development

## Getting Started

### Prerequisites

- Node.js 18 or higher
- npm or pnpm

### Installation

1. Clone the repository:
```bash
git clone https://github.com/whishkid/offlineaidemo.git
cd offlineaidemo
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:5173`

> **Note**: On first load with internet access, the AI models will be downloaded and cached (~50MB). After that, the app works completely offline.

### Building for Production

```bash
npm run build
npm run preview
```

## How It Works

This demo uses [Transformers.js](https://huggingface.co/docs/transformers.js), which is a JavaScript library that allows you to run Hugging Face's transformer models directly in the browser using ONNX Runtime and WebAssembly.

### Model Pipeline

1. **First Load (with internet)**: Models are downloaded from Hugging Face CDN
2. **Caching**: Models are cached in browser storage (IndexedDB)
3. **Offline Mode**: Subsequent loads work without internet
4. **Processing**: All inference happens locally in your browser

### Available Models

- **Sentiment Analysis**: DistilBERT-based model (~65MB) - pre-loaded
- **Text Summarization**: BART-based model (~450MB) - loaded on-demand

### Demo Mode

If the application cannot download models (e.g., no internet connection, blocked CDN), it automatically falls back to a demo mode with mock responses. This allows you to explore the UI and functionality even in restricted environments.

## Project Structure

```
offlineaidemo/
├── src/
│   ├── routes/
│   │   ├── +page.svelte       # Main demo page
│   │   └── +layout.svelte     # Layout component
│   ├── app.css                # Global styles (Tailwind)
│   └── app.html               # HTML template
├── static/                    # Static assets
├── package.json               # Dependencies
├── svelte.config.js           # SvelteKit configuration
├── tailwind.config.js         # Tailwind configuration
├── tsconfig.json              # TypeScript configuration
└── vite.config.ts             # Vite configuration
```

## Usage Examples

### Sentiment Analysis

Try these example texts:
- Positive: "I absolutely love this new feature! It's amazing and works perfectly."
- Negative: "This is terrible and doesn't work at all. Very disappointed."

### Text Summarization

Enter a longer text (3+ sentences) to see the summarization in action. The model will create a concise version while maintaining the key information.

## Deployment

This app can be deployed to:
- **Vercel**: `vercel deploy`
- **Netlify**: Connect your Git repository
- **Cloudflare Pages**: Connect your Git repository
- **Static hosting**: Run `npm run build` and deploy the `.svelte-kit/output` directory

## Performance Considerations

- **Initial Load**: First time loading models takes ~5-30 seconds depending on connection
- **Model Size**: Sentiment model is ~65MB, Summarization is ~450MB
- **Browser Storage**: Models cached in IndexedDB (~500MB total)
- **Inference Speed**: Real-time for sentiment, 2-5 seconds for summarization

## Browser Compatibility

Works on all modern browsers that support:
- WebAssembly
- IndexedDB
- ES2020+

Tested on:
- Chrome 90+
- Firefox 88+
- Safari 14.1+
- Edge 90+

## Limitations

- Models need to be downloaded once before offline use
- Larger models (like summarization) take time to load
- Performance depends on device CPU/memory
- Some browsers may have storage limitations

## Future Enhancements

Possible additions:
- Question Answering model
- Text Translation
- Named Entity Recognition
- Model selection UI
- Progressive Web App (PWA) features
- Service Worker for true offline-first experience

## License

MIT

## Acknowledgments

- [Hugging Face](https://huggingface.co/) for the transformer models
- [Transformers.js](https://github.com/xenova/transformers.js) by @xenova
- [SvelteKit](https://kit.svelte.dev/) team
- [TailwindCSS](https://tailwindcss.com/) team