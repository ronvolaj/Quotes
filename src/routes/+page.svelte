<script>
	import Quote from "#lib/components/Quote.svelte";
</script>

<main
	class="min-h-screen bg-gradient-to-br from-slate-50 via-white to-lime-50 px-4 py-10 sm:py-16 dark:from-slate-950 dark:via-slate-900 dark:to-slate-950"
>
	<div class="mx-auto max-w-6xl">
		<header class="mb-10 sm:mb-14 text-center">
			<span
				class="inline-block rounded-full bg-lime-100 px-4 py-1 text-xs font-semibold uppercase tracking-widest text-lime-700 dark:bg-lime-500/10 dark:text-lime-400"
			>
				Daily Inspiration
			</span>
			<h1
				class="mt-4 bg-gradient-to-r from-slate-900 to-slate-600 bg-clip-text text-3xl font-extrabold tracking-tight text-transparent sm:text-5xl dark:from-white dark:to-slate-400"
			>
				Data Exchange
			</h1>
			<p class="mx-auto mt-4 max-w-xl text-slate-500 dark:text-slate-400">
				Words worth keeping. Copy your favourites or share them with a friend.
			</p>
		</header>

		<section class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
			<Quote author="Regis">Das ist meine Quote</Quote>
			<Quote>Another inspiration for life</Quote>
			<Quote author="Albert Einstein">
				Life is like riding a bicycle. To keep your balance, you must keep moving.
			</Quote>
			<Quote author="Mahatma Gandhi">Be the change that you wish to see in the world.</Quote>
			<Quote author="Oscar Wilde">Be yourself; everyone else is already taken.</Quote>
		</section>
	</div>
</main>