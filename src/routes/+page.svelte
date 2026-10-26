<script>
	import { onMount } from "svelte";
	import { scale } from "svelte/transition";
	import Quote from "#lib/components/Quote.svelte";

	// ---------- Punkt im Schulhof (Shkolla Austriake Shkodër) ----------
	// ⚠️ Durch die echten Koordinaten aus Google Maps ersetzen!
	let target = $state({ lat: 42.0683, lng: 19.5126 });
	const RADIUS = 15; // Meter

	let myPosition = $state(null);
	let distance = $state(null);
	let accuracy = $state(null);
	let geoError = $state("");

	let inRange = $derived(distance !== null && distance <= RADIUS);

	// Abstand zwischen zwei GPS-Punkten in Metern (Haversine-Formel)
	function getDistance(lat1, lng1, lat2, lng2) {
		const R = 6371000;
		const toRad = (deg) => (deg * Math.PI) / 180;
		const dLat = toRad(lat2 - lat1);
		const dLng = toRad(lng2 - lng1);
		const a =
			Math.sin(dLat / 2) ** 2 +
			Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) * Math.sin(dLng / 2) ** 2;
		return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
	}

	function updateDistance() {
		if (myPosition) {
			distance = getDistance(myPosition.lat, myPosition.lng, target.lat, target.lng);
		}
	}

	// Zum Testen: aktuellen Standort als Mittelpunkt setzen
	function setTargetHere() {
		if (!myPosition) return;
		target = { ...myPosition };
		try {
			localStorage.setItem("quoteTarget", JSON.stringify(target));
		} catch {}
		updateDistance();
	}

	onMount(() => {
		try {
			const saved = localStorage.getItem("quoteTarget");
			if (saved) target = JSON.parse(saved);
		} catch {}

		if (!("geolocation" in navigator)) {
			geoError = "Dein Browser unterstützt keine Standortbestimmung.";
			return;
		}

		const watchId = navigator.geolocation.watchPosition(
			(pos) => {
				geoError = "";
				myPosition = { lat: pos.coords.latitude, lng: pos.coords.longitude };
				accuracy = pos.coords.accuracy;
				updateDistance();
			},
			(err) => {
				geoError =
					err.code === err.PERMISSION_DENIED
						? "Standortzugriff verweigert. Bitte in den Browser-Einstellungen erlauben."
						: "Standort konnte nicht ermittelt werden.";
			},
			{ enableHighAccuracy: true, maximumAge: 0, timeout: 15000 }
		);

		return () => navigator.geolocation.clearWatch(watchId);
	});
</script>

<main
	class="min-h-screen bg-gradient-to-br from-slate-50 via-white to-lime-50 px-4 py-10 sm:py-16 dark:from-slate-950 dark:via-slate-900 dark:to-slate-950"
>
	<div class="mx-auto max-w-6xl">
		<header class="mb-10 text-center sm:mb-14">
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

			<!-- Standort-Status -->
			<div class="mt-6 flex flex-col items-center gap-2">
				<div
					class="inline-flex items-center gap-2 rounded-full border px-4 py-2 text-sm font-medium transition
					{inRange
						? 'border-lime-400 bg-lime-50 text-lime-700 dark:bg-lime-500/10 dark:text-lime-400'
						: 'border-slate-200 bg-white/70 text-slate-600 dark:border-slate-700 dark:bg-slate-900/70 dark:text-slate-300'}"
				>
					{#if geoError}
						<span class="text-rose-600">⚠️ {geoError}</span>
					{:else if distance === null}
						<span class="h-2 w-2 animate-pulse rounded-full bg-slate-400"></span>
						Standort wird ermittelt …
					{:else if inRange}
						<span class="relative flex h-2.5 w-2.5">
							<span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-lime-400 opacity-75"></span>
							<span class="relative inline-flex h-2.5 w-2.5 rounded-full bg-lime-500"></span>
						</span>
						Du bist im Schulhof – geheimes Zitat freigeschaltet!
					{:else}
						<span class="h-2.5 w-2.5 rounded-full bg-slate-400"></span>
						Noch {Math.round(distance - RADIUS)} m bis zum geheimen Zitat
					{/if}
				</div>

				{#if myPosition}
					<p class="text-xs text-slate-400">
						Entfernung {Math.round(distance)} m · GPS ±{Math.round(accuracy)} m ·
						<button onclick={setTargetHere} class="underline hover:text-lime-600">
							Hier als Mittelpunkt setzen
						</button>
					</p>
				{/if}
			</div>
		</header>

		<section class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
			<Quote author="Regis">Das ist meine Quote</Quote>
			<Quote>Another inspiration for life</Quote>
			<Quote author="Albert Einstein">
				Life is like riding a bicycle. To keep your balance, you must keep moving.
			</Quote>
			<Quote author="Mahatma Gandhi">Be the change that you wish to see in the world.</Quote>
			<Quote author="Oscar Wilde">Be yourself; everyone else is already taken.</Quote>

			<!-- 6. Zitat: nur im 15-m-Radius sichtbar -->
			{#if inRange}
				<div transition:scale={{ duration: 400, start: 0.8 }} class="rounded-2xl ring-2 ring-lime-400 ring-offset-4 ring-offset-transparent">
					<Quote author="Shkolla Austriake Shkodër">
						Bildung ist der Schlüssel, der jede Tür öffnet.
					</Quote>
				</div>
			{/if}
		</section>
	</div>
</main>