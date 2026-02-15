<script lang="ts">
	import { onMount } from 'svelte';
	import { pipeline, type PipelineType } from '@xenova/transformers';

	let inputText = $state('');
	let result = $state('');
	let loading = $state(false);
	let error = $state('');
	let modelLoaded = $state(false);
	let selectedTask: 'sentiment' | 'summarization' | 'qa' = $state('sentiment');

	let sentimentPipeline: any = null;
	let summarizationPipeline: any = null;

	onMount(() => {
		loadModels();
	});

	async function loadModels() {
		try {
			loading = true;
			error = '';
			
			// Load sentiment analysis model (small and fast)
			sentimentPipeline = await pipeline('sentiment-analysis');
			
			modelLoaded = true;
			loading = false;
		} catch (err) {
			error = 'Failed to load AI models: ' + (err as Error).message;
			loading = false;
		}
	}

	async function analyzeSentiment() {
		if (!inputText.trim()) {
			error = 'Please enter some text';
			return;
		}

		try {
			loading = true;
			error = '';
			result = '';

			const output = await sentimentPipeline(inputText);
			result = `Sentiment: ${output[0].label}\nConfidence: ${(output[0].score * 100).toFixed(2)}%`;
			
			loading = false;
		} catch (err) {
			error = 'Analysis failed: ' + (err as Error).message;
			loading = false;
		}
	}

	async function summarizeText() {
		if (!inputText.trim()) {
			error = 'Please enter some text to summarize';
			return;
		}

		try {
			loading = true;
			error = '';
			result = '';

			// Load summarization model on demand
			if (!summarizationPipeline) {
				summarizationPipeline = await pipeline('summarization');
			}

			const output = await summarizationPipeline(inputText, {
				max_length: 100,
				min_length: 30
			});
			result = `Summary:\n${output[0].summary_text}`;
			
			loading = false;
		} catch (err) {
			error = 'Summarization failed: ' + (err as Error).message;
			loading = false;
		}
	}

	async function processText() {
		if (selectedTask === 'sentiment') {
			await analyzeSentiment();
		} else if (selectedTask === 'summarization') {
			await summarizeText();
		}
	}
</script>

<svelte:head>
	<title>Offline AI Demo</title>
	<meta name="description" content="Offline AI demo using Transformers.js" />
</svelte:head>

<div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 py-12 px-4 sm:px-6 lg:px-8">
	<div class="max-w-4xl mx-auto">
		<!-- Header -->
		<div class="text-center mb-8">
			<h1 class="text-4xl font-bold text-gray-900 mb-2">
				🤖 Offline AI Demo
			</h1>
			<p class="text-lg text-gray-600">
				AI-powered text analysis running entirely in your browser - no internet required!
			</p>
		</div>

		<!-- Status Card -->
		<div class="bg-white rounded-lg shadow-md p-6 mb-6">
			<div class="flex items-center justify-between">
				<div class="flex items-center space-x-3">
					<div class="flex-shrink-0">
						{#if modelLoaded}
							<div class="h-3 w-3 bg-green-500 rounded-full animate-pulse"></div>
						{:else if loading}
							<div class="h-3 w-3 bg-yellow-500 rounded-full animate-pulse"></div>
						{:else}
							<div class="h-3 w-3 bg-red-500 rounded-full"></div>
						{/if}
					</div>
					<div>
						<p class="text-sm font-medium text-gray-900">
							{#if modelLoaded}
								Model Ready
							{:else if loading}
								Loading Model...
							{:else}
								Model Not Loaded
							{/if}
						</p>
						<p class="text-xs text-gray-500">
							Powered by Transformers.js
						</p>
					</div>
				</div>
				<div class="text-right">
					<span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-medium bg-green-100 text-green-800">
						100% Offline
					</span>
				</div>
			</div>
		</div>

		<!-- Main Content Card -->
		<div class="bg-white rounded-lg shadow-md p-6 mb-6">
			<!-- Task Selection -->
			<div class="mb-6">
				<p class="block text-sm font-medium text-gray-700 mb-2">
					Select AI Task
				</p>
				<div class="flex space-x-4">
					<button
						onclick={() => selectedTask = 'sentiment'}
						class="flex-1 py-2 px-4 rounded-lg font-medium transition-colors {selectedTask === 'sentiment' ? 'bg-indigo-600 text-white' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'}"
					>
						Sentiment Analysis
					</button>
					<button
						onclick={() => selectedTask = 'summarization'}
						class="flex-1 py-2 px-4 rounded-lg font-medium transition-colors {selectedTask === 'summarization' ? 'bg-indigo-600 text-white' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'}"
					>
						Text Summarization
					</button>
				</div>
			</div>

			<!-- Input Area -->
			<div class="mb-6">
				<label for="input" class="block text-sm font-medium text-gray-700 mb-2">
					{selectedTask === 'sentiment' ? 'Enter text to analyze' : 'Enter text to summarize'}
				</label>
				<textarea
					id="input"
					bind:value={inputText}
					rows="6"
					class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 resize-none"
					placeholder={selectedTask === 'sentiment' 
						? "e.g., I absolutely love this new feature! It's amazing and works perfectly."
						: "e.g., Enter a longer text here that you want to summarize. The AI will create a concise version..."}
					disabled={loading || !modelLoaded}
				></textarea>
			</div>

			<!-- Action Button -->
			<div class="mb-6">
				<button
					onclick={processText}
					disabled={loading || !modelLoaded || !inputText.trim()}
					class="w-full bg-indigo-600 text-white py-3 px-6 rounded-lg font-medium hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:bg-gray-300 disabled:cursor-not-allowed transition-colors"
				>
					{#if loading}
						<span class="flex items-center justify-center">
							<svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
								<circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
								<path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
							</svg>
							Processing...
						</span>
					{:else}
						{selectedTask === 'sentiment' ? 'Analyze Sentiment' : 'Summarize Text'}
					{/if}
				</button>
			</div>

			<!-- Error Display -->
			{#if error}
				<div class="mb-6 p-4 bg-red-50 border border-red-200 rounded-lg">
					<div class="flex">
						<div class="flex-shrink-0">
							<svg class="h-5 w-5 text-red-400" viewBox="0 0 20 20" fill="currentColor">
								<path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
							</svg>
						</div>
						<div class="ml-3">
							<p class="text-sm text-red-800">{error}</p>
						</div>
					</div>
				</div>
			{/if}

			<!-- Result Display -->
			{#if result}
				<div class="p-6 bg-gradient-to-r from-green-50 to-blue-50 border border-green-200 rounded-lg">
					<h3 class="text-lg font-semibold text-gray-900 mb-3">Result</h3>
					<div class="bg-white p-4 rounded-lg shadow-sm">
						<pre class="text-gray-800 whitespace-pre-wrap font-mono text-sm">{result}</pre>
					</div>
				</div>
			{/if}
		</div>

		<!-- Info Card -->
		<div class="bg-white rounded-lg shadow-md p-6">
			<h2 class="text-xl font-semibold text-gray-900 mb-4">About This Demo</h2>
			<div class="space-y-3 text-gray-600">
				<p>
					This application demonstrates offline AI capabilities using <strong>Transformers.js</strong>, 
					which allows you to run machine learning models directly in your browser.
				</p>
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
					<div class="flex items-start space-x-3">
						<span class="text-2xl">🚀</span>
						<div>
							<h3 class="font-semibold text-gray-900">Fast</h3>
							<p class="text-sm">Models run locally using WebAssembly</p>
						</div>
					</div>
					<div class="flex items-start space-x-3">
						<span class="text-2xl">🔒</span>
						<div>
							<h3 class="font-semibold text-gray-900">Private</h3>
							<p class="text-sm">Your data never leaves your device</p>
						</div>
					</div>
					<div class="flex items-start space-x-3">
						<span class="text-2xl">📡</span>
						<div>
							<h3 class="font-semibold text-gray-900">Offline</h3>
							<p class="text-sm">Works without internet after initial load</p>
						</div>
					</div>
					<div class="flex items-start space-x-3">
						<span class="text-2xl">⚡</span>
						<div>
							<h3 class="font-semibold text-gray-900">Lightweight</h3>
							<p class="text-sm">Uses optimized ONNX models</p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
