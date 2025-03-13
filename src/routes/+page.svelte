<script lang="ts">
	import { onMount } from 'svelte';

	let pokemonNumber = 1;
	let pokemonSearchName = ''; // Nome para pesquisa
	let generation = 'front_default';
	let backgroundColor = '#000';
	let pokemonName = '';
	let spriteUrl = '';
	let loading = false;
	let error = '';
	let canvasElement: HTMLCanvasElement;
	let spriteScale = 3; // Escala inicial do sprite

	// Função para aumentar a escala
	function increaseScale() {
		if (spriteScale < 10) spriteScale++; // Limitado a 10x
	}

	// Função para diminuir a escala
	function decreaseScale() {
		if (spriteScale > 1) spriteScale--; // Mínimo de 1x
	}

	const generations = [
		{ id: 'front_default', name: 'Sprite Default (Frente)' },
		{ id: 'front_shiny', name: 'Sprite Shiny (Frente)' },
		{ id: 'back_default', name: 'Sprite Default (Costas)' },
		{ id: 'back_shiny', name: 'Sprite Shiny (Costas)' },
		{ id: 'versions/generation-i/red-blue/front_default', name: 'Sprite Gen 1 (Red/Blue)' },
		{ id: 'versions/generation-ii/crystal/front_default', name: 'Sprite Gen 2 (Crystal)' },
		{ id: 'versions/generation-iii/emerald/front_default', name: 'Sprite Gen 3 (Emerald)' },
		{
			id: 'versions/generation-iv/diamond-pearl/front_default',
			name: 'Sprite Gen 4 (Diamond/Pearl)'
		},
		{ id: 'versions/generation-v/black-white/front_default', name: 'Sprite Gen 5 (Black/White)' },
		{
			id: 'versions/generation-v/black-white/animated/front_default',
			name: 'Sprite Gen 5 (Animado)'
		},
		{ id: 'versions/generation-vi/x-y/front_default', name: 'Sprite Gen 6 (X/Y)' },
		{
			id: 'versions/generation-vii/ultra-sun-ultra-moon/front_default',
			name: 'Sprite Gen 7 (Ultra)'
		},
		{ id: 'versions/generation-viii/icons/front_default', name: 'Ícone Gen 8' }
	];

	onMount(() => {
		fetchPokemonData();
	});

	// Função para buscar por nome
	async function fetchPokemonByName() {
		if (!pokemonSearchName.trim()) {
			error = 'Digite um nome de Pokémon';
			return;
		}

		loading = true;
		error = '';

		try {
			// Nome para minúsculo para compatibilidade com a API
			const searchName = pokemonSearchName.toLowerCase().trim();
			const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${searchName}`);

			if (!response.ok) {
				throw new Error('Pokémon não encontrado');
			}

			const data = await response.json();

			// Atualiza o número do Pokémon para refletir o resultado encontrado
			pokemonNumber = data.id;

			// Continua com o processamento dos dados do Pokémon
			processResults(data);
		} catch (err: any) {
			error = err.message || 'Ocorreu um erro ao buscar o Pokémon';
			spriteUrl = '';
		} finally {
			loading = false;
		}
	}

	async function fetchPokemonData() {
		loading = true;
		error = '';

		try {
			const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonNumber}`);

			if (!response.ok) {
				throw new Error('Pokémon não encontrado');
			}

			const data = await response.json();

			// Processar os dados do Pokémon
			processResults(data);
		} catch (err: any) {
			error = err.message || 'Ocorreu um erro ao buscar o Pokémon';
			spriteUrl = '';
		} finally {
			loading = false;
		}
	}

	// Função para processar os resultados da API
	function processResults(data: any) {
		pokemonName = data.name.charAt(0).toUpperCase() + data.name.slice(1);

		// Atualiza o campo de nome para refletir o nome encontrado
		pokemonSearchName = pokemonName;

		// Obter o URL da sprite com base na geração selecionada
		let url = '';
		const pathParts = generation.split('/');

		if (pathParts.length === 1) {
			url = data.sprites[generation];
		} else {
			let spriteObj = data.sprites;
			for (const part of pathParts) {
				if (spriteObj && spriteObj[part]) {
					spriteObj = spriteObj[part];
				} else {
					spriteObj = null;
					break;
				}
			}
			url = spriteObj;
		}

		if (!url) {
			throw new Error('Sprite não disponível para este Pokémon nesta geração');
		}

		spriteUrl = url;
	}

	function exportImage() {
		if (!spriteUrl) return;

		const canvas = canvasElement;
		const ctx = canvas.getContext('2d');

		if (!ctx) return;

		canvas.width = 600;
		canvas.height = 600;

		ctx.fillStyle = backgroundColor;
		ctx.fillRect(0, 0, canvas.width, canvas.height);

		const img = new Image();
		img.crossOrigin = 'Anonymous';
		img.onload = () => {
			// Calcular posição para centralizar o sprite
			const x = (canvas.width - img.width * spriteScale) / 2;
			const y = (canvas.height - img.height * spriteScale) / 2;

			// Desenhar o sprite em tamanho ampliado
			ctx.imageSmoothingEnabled = false; // Desativar suavização para manter pixels nítidos
			ctx.drawImage(img, x, y, img.width * spriteScale, img.height * spriteScale);

			// Criar link de download
			const link = document.createElement('a');
			link.download = `pokemon-${pokemonName.toLowerCase()}-${pokemonNumber}.png`;
			link.href = canvas.toDataURL('image/png');
			link.click();
		};
		img.src = spriteUrl;
	}

	// Função para lidar com a tecla Enter no campo de busca por nome
	function handleKeyPress(event: KeyboardEvent) {
		if (event.key === 'Enter') {
			fetchPokemonByName();
		}
	}
</script>

<main class="container mx-auto max-w-3xl p-4">
	<h1 class="mb-6 text-center text-3xl font-bold">Visualizador de Sprites Pokémon</h1>

	<div class="flex flex-col gap-6">
		<div class="rounded-lg bg-gray-100 p-4">
			<div class="grid grid-cols-1 gap-4 md:grid-cols-2">
				<div>
					<label for="pokemon-number" class="mb-1 block text-sm font-medium"
						>Número do Pokémon:</label
					>
					<div class="flex gap-2">
						<input
							type="number"
							id="pokemon-number"
							bind:value={pokemonNumber}
							min="1"
							max="1025"
							class="w-full rounded border px-3 py-2"
						/>
						<button
							on:click={fetchPokemonData}
							class="rounded bg-blue-500 px-4 py-2 text-white hover:bg-blue-600"
							disabled={loading}
						>
							Buscar
						</button>
					</div>
				</div>

				<div>
					<label for="pokemon-name" class="mb-1 block text-sm font-medium">Nome do Pokémon:</label>
					<div class="flex gap-2">
						<input
							type="text"
							id="pokemon-name"
							bind:value={pokemonSearchName}
							on:keypress={handleKeyPress}
							placeholder="Ex: pikachu, charizard..."
							class="w-full rounded border px-3 py-2"
						/>
						<button
							on:click={fetchPokemonByName}
							class="rounded bg-blue-500 px-4 py-2 text-white hover:bg-blue-600"
							disabled={loading}
						>
							Buscar
						</button>
					</div>
				</div>

				<div class="md:col-span-2">
					<label for="generation" class="mb-1 block text-sm font-medium">Tipo de Sprite:</label>
					<select
						id="generation"
						bind:value={generation}
						on:change={fetchPokemonData}
						class="w-full rounded border px-3 py-2"
					>
						{#each generations as gen}
							<option value={gen.id}>{gen.name}</option>
						{/each}
					</select>
				</div>
			</div>
		</div>

		<div class="flex flex-col gap-6 md:flex-row">
			<div class="flex-1">
				{#if loading}
					<div class="sprite-container flex items-center justify-center rounded-lg bg-gray-100">
						<p>Carregando...</p>
					</div>
				{:else if error}
					<div class="sprite-container flex items-center justify-center rounded-lg bg-gray-100">
						<p class="text-red-500">{error}</p>
					</div>
				{:else if spriteUrl}
					<div class="flex flex-col items-center">
						<h2 class="mb-2 text-xl font-semibold">#{pokemonNumber} - {pokemonName}</h2>

						<div class="mb-2 flex items-center justify-center space-x-4">
							<button
								on:click={decreaseScale}
								class="h-8 w-8 rounded-full bg-gray-300 text-center text-lg font-bold hover:bg-gray-400"
								disabled={spriteScale <= 1}
							>
								-
							</button>
							<span class="text-sm font-medium">Zoom: {spriteScale}x</span>
							<button
								on:click={increaseScale}
								class="h-8 w-8 rounded-full bg-gray-300 text-center text-lg font-bold hover:bg-gray-400"
								disabled={spriteScale >= 10}
							>
								+
							</button>
						</div>

						<div
							class="sprite-view relative flex aspect-square w-full items-center justify-center rounded-lg"
							style="background-color: {backgroundColor};"
						>
							<div class="sprite-container-wrapper overflow-hidden">
								<img
									src={spriteUrl}
									alt={pokemonName}
									class="pixel-art pointer-events-none object-contain"
									style="transform: scale({spriteScale});"
								/>
							</div>
						</div>
					</div>
				{:else}
					<div class="sprite-container flex items-center justify-center rounded-lg bg-gray-100">
						<p>Selecione um Pokémon para visualizar</p>
					</div>
				{/if}
			</div>

			<div class="flex-1">
				<div class="rounded-lg bg-gray-100 p-4">
					<h3 class="mb-3 text-lg font-semibold">Personalização</h3>

					<div class="mb-4">
						<label for="background-color" class="mb-1 block text-sm font-medium"
							>Cor de Fundo:</label
						>
						<div class="flex gap-2">
							<input
								type="color"
								id="background-color"
								bind:value={backgroundColor}
								class="h-10 w-16"
							/>
							<input
								type="text"
								bind:value={backgroundColor}
								class="flex-1 rounded border px-3 py-2"
							/>
						</div>
					</div>

					<button
						on:click={exportImage}
						class="w-full rounded bg-green-500 px-4 py-2 text-white hover:bg-green-600"
						disabled={!spriteUrl}
					>
						Exportar Imagem (600x600)
					</button>
				</div>

				<!-- Canvas invisível para exportação -->
				<canvas bind:this={canvasElement} class="hidden"></canvas>
			</div>
		</div>
	</div>
</main>

<style>
	input[type='number']::-webkit-inner-spin-button,
	input[type='number']::-webkit-outer-spin-button {
		opacity: 1;
	}

	/* Estilo para preservar a qualidade dos pixels em sprites ampliados */
	.pixel-art {
		image-rendering: pixelated;
		image-rendering: -moz-crisp-edges;
		image-rendering: crisp-edges;
	}

	/* Container para os sprites */
	.sprite-container {
		height: 420px;
	}

	/* Wrapper para conter o sprite e evitar que ele se expanda além de seus limites */
	.sprite-container-wrapper {
		height: 100%;
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
	}

	/* Visão do sprite com overflow escondido */
	.sprite-view {
		height: 350px;
		overflow: hidden;
	}
</style>
