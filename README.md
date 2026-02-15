# Offline AI Demo 🤖

A demonstration of offline AI capabilities using SvelteKit and Transformers.js. This application runs machine learning models entirely in your browser without requiring an internet connection.

## Features

- 🚀 **Sentiment Analysis**: Analyze the emotional tone of text
- 📝 **Text Summarization**: Generate concise summaries of longer texts
- 🔒 **Privacy-First**: All processing happens locally on your device
- 📡 **Fully Offline**: Works without internet after initial model download
- ⚡ **Fast & Lightweight**: Uses optimized ONNX models via WebAssembly

## Technologies Used

- **SvelteKit**: Modern web framework for building the UI
- **Transformers.js**: Run Hugging Face models in the browser
- **TailwindCSS**: Utility-first CSS framework for styling
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

### Building for Production

```bash
npm run build
npm run preview
```

## How It Works

This demo uses [Transformers.js](https://huggingface.co/docs/transformers.js), which is a JavaScript library that allows you to run Hugging Face's transformer models directly in the browser using ONNX Runtime. The models are downloaded once and cached locally, enabling true offline functionality.

### Available Models

- **Sentiment Analysis**: DistilBERT-based model for classifying text as positive or negative
- **Text Summarization**: BART-based model for generating summaries (loaded on-demand)

## License

MIT