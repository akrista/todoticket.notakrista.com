<script lang="ts">
	import { onMount } from "svelte";

	let total: number | null = null;
	let retiroOptimo: number = 0;
	let comision: number = 0;
	let restante: number = 0;

	onMount(() => {
		const prefersDark = window.matchMedia(
			"(prefers-color-scheme: dark)",
		).matches;
		if (prefersDark) {
			document.documentElement.classList.add("dark");
		} else {
			document.documentElement.classList.remove("dark");
		}

		const mediaQuery = window.matchMedia("(prefers-color-scheme: dark)");
		const handleSystemChange = (e: MediaQueryListEvent) => {
			if (e.matches) {
				document.documentElement.classList.add("dark");
			} else {
				document.documentElement.classList.remove("dark");
			}
		};
		mediaQuery.addEventListener("change", handleSystemChange);

		return () => {
			mediaQuery.removeEventListener("change", handleSystemChange);
		};
	});

	function calculate() {
		if (total === null || total <= 0) {
			retiroOptimo = 0;
			comision = 0;
			restante = 0;
			return;
		}

		retiroOptimo = Math.floor((total / 1.006) * 100) / 100;

		let iterations = 0;
		const maxIterations = 10;

		while (iterations < maxIterations) {
			comision = Math.round(retiroOptimo * 0.006 * 100) / 100;

			restante = total - (comision + retiroOptimo);

			restante = Math.round(restante * 100) / 100;

			if (restante <= 0 || Math.abs(restante) < 0.001) {
				restante = 0;
				break;
			}
			if (Object.is(restante, -0)) {
				restante = 0;
				break;
			}

			const nuevoRetiroOptimo = retiroOptimo + restante;
			let nuevoRetiroOptimoRedondeado =
				Math.floor(nuevoRetiroOptimo * 100) / 100;

			if (nuevoRetiroOptimoRedondeado <= retiroOptimo && restante > 0) {
				nuevoRetiroOptimoRedondeado =
					Math.ceil(nuevoRetiroOptimo * 100) / 100;
			}

			retiroOptimo = nuevoRetiroOptimoRedondeado;

			iterations++;
		}

		comision = Math.round(retiroOptimo * 0.006 * 100) / 100;
		restante = total - (comision + retiroOptimo);
		restante = Math.round(restante * 100) / 100;
		if (restante <= 0 || Math.abs(restante) < 0.001) {
			restante = 0;
		}
		if (Object.is(restante, -0)) {
			restante = 0;
		}
	}

	function formatCurrency(value: number): string {
		const normalizedValue = value === 0 ? 0 : value;
		return new Intl.NumberFormat("es-VE", {
			minimumFractionDigits: 2,
			maximumFractionDigits: 2,
		}).format(normalizedValue);
	}

	function handleInputChange(event: Event) {
		const target = event.target as HTMLInputElement;
		const value = parseFloat(target.value);
		total = isNaN(value) ? null : value;
		calculate();
	}
</script>

<div
	class="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-200"
>
	<!-- Header -->
	<header
		class="bg-todo-orange dark:bg-todo-orange/90 text-white p-4 shadow-lg"
	>
		<div class="container mx-auto flex justify-center items-center">
			<h1 class="text-2xl font-bold">Calculadora Todoticket</h1>
		</div>
	</header>

	<!-- Main Content -->
	<main class="container mx-auto px-4 py-8 max-w-2xl">
		<div
			class="bg-white dark:bg-gray-800 rounded-lg shadow-xl p-6 md:p-8 space-y-6 border border-gray-200 dark:border-gray-700"
		>
			<!-- Input Section -->
			<div class="space-y-2">
				<label
					for="total"
					class="block text-lg font-semibold text-gray-700 dark:text-gray-200 leading-tight"
				>
					Total Disponible
				</label>
				<div class="relative">
					<span
						class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-500 dark:text-gray-400"
					>
						Bs.
					</span>
					<input
						id="total"
						type="number"
						step="0.01"
						min="0"
						placeholder="0.00"
						oninput={handleInputChange}
						class="w-full pl-12 pr-4 py-3 text-lg border-2 border-gray-300 dark:border-gray-600 rounded-lg focus:outline-none focus:border-todo-orange dark:bg-gray-700 dark:text-white transition-colors"
					/>
				</div>
			</div>

			<!-- Results Section -->
			{#if total !== null && total > 0}
				<div
					class="space-y-4 pt-4 border-t border-gray-200 dark:border-gray-700"
				>
					<!-- Restante -->
					<div
						class="bg-blue-50 dark:bg-blue-900/20 p-4 rounded-lg border-l-4 border-todo-blue"
					>
						<div class="flex justify-between items-center">
							<div>
								<p
									class="text-sm text-gray-600 dark:text-gray-400"
								>
									Restante
								</p>
								<p
									class="text-2xl font-bold {restante > 0
										? 'text-todo-blue dark:text-blue-300'
										: 'text-gray-600 dark:text-gray-400'}"
								>
									Bs. {formatCurrency(restante)}
								</p>
							</div>
							<div
								class="w-12 h-12 rounded-full {restante > 0
									? 'bg-todo-blue'
									: 'bg-gray-400'} flex items-center justify-center"
							>
								<svg
									class="w-6 h-6 text-white"
									fill="none"
									stroke="currentColor"
									viewBox="0 0 24 24"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
									/>
								</svg>
							</div>
						</div>
					</div>

					<!-- Comisión -->
					<div
						class="bg-red-50 dark:bg-red-900/20 p-4 rounded-lg border-l-4 border-todo-red"
					>
						<div class="flex justify-between items-center">
							<div>
								<p
									class="text-sm text-gray-600 dark:text-gray-400"
								>
									Comisión
								</p>
								<p
									class="text-2xl font-bold text-todo-red dark:text-red-300"
								>
									Bs. {formatCurrency(comision)}
								</p>
							</div>
							<div
								class="w-12 h-12 rounded-full bg-todo-red flex items-center justify-center"
							>
								<svg
									class="w-6 h-6 text-white"
									fill="none"
									stroke="currentColor"
									viewBox="0 0 24 24"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"
									/>
								</svg>
							</div>
						</div>
					</div>

					<!-- Retiro Óptimo -->
					<div
						class="bg-green-50 dark:bg-green-900/20 p-4 rounded-lg border-l-4 border-todo-green"
					>
						<div class="flex justify-between items-center">
							<div>
								<p
									class="text-sm text-gray-600 dark:text-gray-400"
								>
									Retiro Óptimo
								</p>
								<p
									class="text-2xl font-bold text-todo-green dark:text-green-300"
								>
									Bs. {formatCurrency(retiroOptimo)}
								</p>
							</div>
							<div
								class="w-12 h-12 rounded-full bg-todo-green flex items-center justify-center"
							>
								<svg
									class="w-6 h-6 text-white"
									fill="none"
									stroke="currentColor"
									viewBox="0 0 24 24"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
									/>
								</svg>
							</div>
						</div>
					</div>

					<!-- Info Message -->
					{#if restante <= 0}
						<div
							class="bg-green-100 dark:bg-green-900/30 border border-green-300 dark:border-green-700 text-green-800 dark:text-green-200 px-4 py-3 rounded-lg"
						>
							<p class="text-sm font-medium">
								✓ Puedes retirar la totalidad de tus fondos sin
								dejar saldo restante.
							</p>
						</div>
					{:else}
						<div
							class="bg-yellow-100 dark:bg-yellow-900/30 border border-yellow-300 dark:border-yellow-700 text-yellow-800 dark:text-yellow-200 px-4 py-3 rounded-lg"
						>
							<p class="text-sm font-medium">
								⚠ Quedará un pequeño saldo restante en tu
								cuenta.
							</p>
						</div>
					{/if}
				</div>
			{/if}

			<!-- Instructions -->
			<div class="pt-6 border-t border-gray-200 dark:border-gray-700">
				<div class="bg-gray-50 dark:bg-gray-700/50 p-4 rounded-lg">
					<h3
						class="font-semibold text-gray-700 dark:text-gray-200 mb-2"
					>
						¿Cómo funciona?
					</h3>
					<ul
						class="text-sm text-gray-600 dark:text-gray-400 space-y-1 list-disc list-inside"
					>
						<li>
							Ingresa el monto total disponible en tu cuenta
							Todoticket
						</li>
						<li>
							La calculadora determinará el retiro óptimo para
							minimizar el saldo restante
						</li>
						<li>
							Se aplica una comisión del 0.6% sobre el monto
							retirado
						</li>
						<li>
							El objetivo es retirar la mayor cantidad posible sin
							dejar saldo
						</li>
					</ul>
				</div>
			</div>
		</div>
	</main>

	<!-- Footer -->
	<footer
		class="mt-12 py-6 text-center text-gray-600 dark:text-gray-400 text-sm"
	>
		<p>
			Hecho por <a
				href="https://notakrista.com"
				class="text-todo-orange dark:text-todo-orange/80 hover:text-todo-orange/90 transition-colors"
				>Akrista</a
			>
		</p>
	</footer>
</div>
