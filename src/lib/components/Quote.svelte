<script>
	let { children, author = "Unknown" } = $props();

	let quoteElement;
	let copied = $state(false);
	let shared = $state(false);
	const canShare = typeof navigator !== "undefined" && !!navigator.share;

	const initials = $derived(
		author
			.split(" ")
			.map((w) => w[0])
			.join("")
			.slice(0, 2)
			.toUpperCase()
	);

	const text = () => quoteElement.innerText.trim();

	async function copyQuote() {
		await navigator.clipboard.writeText(`"${text()}" — ${author}`);
		copied = true;
		setTimeout(() => (copied = false), 2000);

		if ("Notification" in window) {
			let permission = Notification.permission;
			if (permission === "default") permission = await Notification.requestPermission();
			if (permission === "granted") {
				new Notification("Quote copied to clipboard!", {
					body: "You successfully copied the quote to your clipboard."
				});
			}
		}
	}

	async function share() {
		try {
			await navigator.share({ title: "Please read my quote", text: `"${text()}" — ${author}` });
			shared = true;
			setTimeout(() => (shared = false), 2000);
		} catch {
			/* user cancelled */
		}
	}
</script>

<figure
	class="group relative flex flex-col justify-between overflow-hidden rounded-2xl border border-slate-200 bg-white p-6 sm:p-8 shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-xl hover:shadow-lime-500/10 dark:border-slate-800 dark:bg-slate-900"
>
	<!-- accent bar -->
	<div
		class="absolute inset-x-0 top-0 h-1 bg-gradient-to-r from-lime-400 via-emerald-400 to-teal-400 opacity-70 transition group-hover:opacity-100"
	></div>

	<!-- decorative quote mark -->
	<svg
		class="absolute right-6 top-6 h-16 w-16 text-lime-500/10 transition group-hover:text-lime-500/20"
		viewBox="0 0 24 24"
		fill="currentColor"
		aria-hidden="true"
	>
		<path
			d="M9.5 6C6.5 6 4 8.5 4 11.5V18h6v-6H7c0-1.7 1.3-3 3-3V6h-.5zm10 0c-3 0-5.5 2.5-5.5 5.5V18h6v-6h-3c0-1.7 1.3-3 3-3V6h-.5z"
		/>
	</svg>

	<blockquote
		bind:this={quoteElement}
		class="relative pr-10 font-serif text-lg sm:text-xl leading-relaxed text-slate-700 dark:text-slate-200"
	>
		{@render children?.()}
	</blockquote>

	<figcaption class="mt-6 sm:mt-8 flex flex-wrap items-center justify-between gap-4">
		<div class="flex items-center gap-3">
			<div
				class="flex h-10 w-10 shrink-0 items-center justify-center rounded-full bg-gradient-to-br from-lime-400 to-emerald-500 text-sm font-bold text-white shadow-md"
			>
				{initials || "?"}
			</div>
			<div>
				<p class="font-semibold text-slate-900 dark:text-white">{author}</p>
				<p class="text-xs uppercase tracking-wider text-slate-400">Author</p>
			</div>
		</div>

		<div class="flex gap-2">
			<button
				onclick={copyQuote}
				aria-label="Copy quote"
				title="Copy quote"
				class="flex h-10 items-center gap-2 rounded-full border border-slate-200 px-3 text-sm font-medium text-slate-600 transition hover:border-lime-500 hover:bg-lime-50 hover:text-lime-700 active:scale-95 dark:border-slate-700 dark:text-slate-300 dark:hover:bg-lime-500/10"
			>
				{#if copied}
					<svg class="h-4 w-4 text-lime-600" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>
					<span class="text-lime-600">Copied!</span>
				{:else}
					<svg class="h-4 w-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="9" y="9" width="13" height="13" rx="2" /><path d="M5 15H4a2 2 0 01-2-2V4a2 2 0 012-2h9a2 2 0 012 2v1" /></svg>
					<span class="hidden sm:inline">Copy</span>
				{/if}
			</button>

			{#if canShare}
				<button
					onclick={share}
					aria-label="Share quote"
					title="Share quote"
					class="flex h-10 items-center gap-2 rounded-full bg-lime-500 px-3 text-sm font-medium text-white shadow-md shadow-lime-500/30 transition hover:bg-lime-600 active:scale-95"
				>
					<svg class="h-4 w-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="18" cy="5" r="3" /><circle cx="6" cy="12" r="3" /><circle cx="18" cy="19" r="3" /><path d="M8.6 13.5l6.8 4M15.4 6.5l-6.8 4" /></svg>
					<span class="hidden sm:inline">{shared ? "Shared!" : "Share"}</span>
				</button>
			{/if}
		</div>
	</figcaption>
</figure>