<script lang="ts">
	import { fade } from 'svelte/transition';

	let nama = '';
	let audioFile: File | null = null;
	let audioURL = '';
	let verified = false;
	let currentStage = 0;
	let currentIndex = 0;
	let show = true;
	let tebakJawaban = '';
	let tebakSalah = 0;
	let showLetter = false;

	const jawabanTebakan = 'karena manis';
	const pengantar = [
		"Hai {nama} 🌷",
		"Sebelum baca suratnya, jawab ini dulu ya~"
	];

	const tebakQuestion = "Kenapa permen nggak pernah galau?";
	const tebakResponses = [
		"Hmmm... mikir dulu dong jangan asal 😜",
		"Salah lagi nih... ya ampun kamu lucu deh 😆",
		"Ishhh masih salah... kamu emang ngeselin tapi gemesin 😝"
	];

	const messages = [
		"halooooo {nama},",
		"i know ini mungkin nggak seberapa...",
		"tapi aku buat ini dengan hatiku",
		"aku cuma mau ngomong sesuatu...",
		"MAKASIHHHHH AYAAAA",
		"kadanggg, kalo aku lagi gundah",
		"pasti kamu bisa bikin aku senyum lagi",
		"kamu itu baik, penyayang, cantik lagi",
		"kadang kalo liat ke randoman mu",
		"bisa bikin aku ngakak",
		"jaga pola makanmu, tetep sehat, sm tetep semangat",
		"{nama} kan cewe yang kuat",
		"lagu ini, waktu aku dengerin",
		"yang ada di pikiranku cuma kamu, asekkk",
		"ohh iya keinget aku",
		"kalo kata mas Paul Klein",
		"cantikmu gabisa dilukis even oleh Michaelangelo",
		"ehhh udah panjang nih, makasih untuk waktunyaa",
	];

	function verifyName() {
		if (nama.trim().toLowerCase() === 'aya') {
			verified = true;
		} else {
			alert('HMMM.... siapa kamu??? 😅');
		}
	}

	function startTebakan() {
		if (tebakJawaban.toLowerCase().trim() === jawabanTebakan) {
			playMusicAndStartLetter();
		} else {
			tebakSalah++;
		}
	}

	function playMusicAndStartLetter() {
		audioURL = audioFile ? URL.createObjectURL(audioFile) : './audio/bgm.mp3';
		showLetter = true;

		setTimeout(() => {
			const audio = document.getElementById('bgm') as HTMLAudioElement;
			audio?.play();
		}, 200);

		const interval = setInterval(() => {
			show = false;
			setTimeout(() => {
				currentIndex++;
				if (currentIndex < messages.length) {
					show = true;
				} else {
					clearInterval(interval);
				}
			}, 500);
		}, 4000);
	}
</script>

{#if !verified}
	<!-- Nama Verifikasi -->
	<div class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-br from-pink-100 via-rose-200 to-pink-300 px-4">
		<h1 class="text-3xl font-bold text-gray-700 mb-6">Siapa namamu? 😗</h1>
		<div class="bg-white/60 backdrop-blur-lg p-8 rounded-3xl shadow-xl w-full max-w-md space-y-4">
			<input
				type="text"
				bind:value={nama}
				placeholder="Masukkan nama kamu"
				class="input rounded-lg input-bordered w-full"
			/>
			<button on:click={verifyName} class="btn bg-pink-900 btn-secondary w-full text-pink-300 py-1 rounded-md">Lanjut</button>
		</div>
	</div>

{:else if currentStage < pengantar.length}
	<!-- Kalimat Pengantar -->
	<div class="min-h-screen flex items-center justify-center bg-rose-100 px-4">
		<div
			in:fade={{ duration: 600 }}
			out:fade={{ duration: 400 }}
			class="bg-white/80 backdrop-blur-md px-8 py-6 rounded-3xl shadow-xl text-xl max-w-lg text-center font-medium text-gray-700"
		>
			{@html pengantar[currentStage].replace('{nama}', `<span class="text-pink-500 font-bold">${nama}</span>`)}
			<button on:click={() => currentStage++} class="btn bg-pink-900 btn-secondary w-full text-pink-300 py-1 rounded-md">Lanjut</button>
		</div>
	</div>

{:else if !showLetter && tebakSalah < 3}
	<!-- Tebakan -->
	<div class="min-h-screen flex flex-col items-center justify-center bg-pink-200 px-4 text-center">
		<h2 class="text-xl font-semibold mb-4">{tebakQuestion}</h2>
		<input
			bind:value={tebakJawaban}
			placeholder="Jawabanmu..."
			class="input input-bordered rounded-sm outline-pink-800 w-full max-w-sm mb-4"
		/>
		<button on:click={startTebakan} class="btn bg-pink-900 btn-secondary w-25 text-pink-300 py-1 rounded-md">Jawab</button>

		{#if tebakSalah > 0}
			<p class="mt-4 text-pink-600 italic">{tebakResponses[tebakSalah - 1]}</p>
		{/if}
	</div>

{:else if tebakSalah >= 3 && !showLetter}
	<!-- Gagal Tebakan -->
	<div class="min-h-screen flex flex-col items-center justify-center bg-rose-300 px-4 text-center">
		<h2 class="text-2xl font-bold text-white mb-4">Yahhahahaha gagal terus 😜</h2>
		<p class="text-white text-lg mb-6">Kamu emang nggak jago tebak-tebakan, tapi jago ngebuat aku senyum🥰</p>

		<div class="w-full max-w-sm space-y-4">
			<input
				type="file"
				accept="audio/*"
				on:change={(e) => audioFile = e.target.files?.[0] || null}
				class="file-input file-input-bordered w-full"
			/>
			<button on:click={playMusicAndStartLetter} class="btn btn-secondary w-full">
				Lanjut ke Surat 💌
			</button>
		</div>
	</div>

{:else}
	<!-- LOVE LETTER -->
	<audio id="bgm" src={audioURL} autoplay loop preload="auto" />

	<div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-pink-100 via-rose-200 to-pink-300 px-4">
		{#if currentIndex < messages.length}
			{#if show}
				<div
					in:fade={{ duration: 500 }}
					out:fade={{ duration: 500 }}
					class="bg-white/70 backdrop-blur-md px-8 py-6 rounded-3xl shadow-xl text-xl max-w-lg text-center font-medium text-gray-800"
				>
					{@html messages[currentIndex].replace("{nama}", `<span class="font-bold text-pink-600">${nama}</span>`)}
				</div>
			{/if}
		{:else}
			<div class="text-white text-2xl font-semibold text-center">💌 Udah selesai ya... semoga {nama} suka 😚</div>
		{/if}
	</div>
{/if}
