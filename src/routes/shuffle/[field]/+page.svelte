<script>
	import { currentPlatform, currentAudience, currentChaos } from '$lib/shared/stores.js';
	import { getStores, navigating, page, updated } from '$app/stores';

	import Filler from '$lib/components/others/Filler.svelte';
	import PlusButton from '$lib/components/button/PlusButton.svelte';
	import Button from '$lib/components/button/Button.svelte';
	import { fade } from 'svelte/transition';
	import Modal from '$lib/components/modal/Modal.svelte';
	import Static from '$lib/components/others/Static.svelte';
	import CopyLinkButton from '$lib/components/button/CopyLinkButton.svelte';
	import slugify from 'slugify';
	import { beforeUpdate } from 'svelte';
	import ShufflerModule from '$lib/components/shuffle/ShuffleModule.svelte';

	/**
	 * @type {{ json: { platforms: any; audiences: any; chaosModifiers: any; }; }}
	 */
	export let data;

	let isHidden = true;
	let showModal = false;
	let chaos = false;
	let filler = true;
	let context = $page.params.field;
	let paused = true;

	const enableChaos = () => {
		chaos = true;
		paused = false;
	};

	const enableShuffler = () => {
		paused = false;
		isHidden = false;
	};

	beforeUpdate(() => {
		context = $page.params.field;
	});

	const share = () => {
		showModal = true;
	};

	/**
	 * @param {string | undefined} text
	 */
	function copyToClipboard(text) {
		window.prompt('Copy to clipboard: Ctrl+C, Enter', text);
	}

	const copyLink = () => {
		let audienceSlug = '';
		let platformSlug = '';
		let chaosSlug = '';

		if ($currentAudience) {
			audienceSlug = slugify($currentAudience);
		}

		if ($currentPlatform) {
			platformSlug = slugify($currentPlatform);
		}

		if ($currentChaos) {
			chaosSlug = slugify($currentChaos);
		}

		let link = `${$page.url.origin}/share/${$page.params.field}/${audienceSlug}+${platformSlug}+${chaosSlug}`;

		copyToClipboard(link);
	};
</script>

<div class="shuffle_container">
	<ShufflerModule {filler} {context} {data} {isHidden} {chaos} {enableShuffler} {paused} />
</div>

{#if showModal}
	<div transition:fade>
		<Modal on:close={() => (showModal = false)}>
			<h2 slot="header">Well well it looks like iam going to...</h2>
			<div slot="content">
				<div class="share_container">
					<Filler title="Design" />
					<Static title={$currentPlatform} color="green" {filler} />
					<Filler title="For" />
					<Static title={$currentAudience} color="purple" />
					{#if chaos}
						<Static title={$currentChaos} color="yellow" />
					{/if}
				</div>

				<div class="button_container">
					<CopyLinkButton title="Copy link" {copyLink} />
				</div>
			</div>
		</Modal>
	</div>
{/if}

<footer>
	<div transition:fade>
		{#if chaos === false && isHidden === false}
			<PlusButton {enableChaos} {paused} title="Add chaos" />
		{/if}
	</div>
	<div>
		{#if isHidden === false}
			<Button title="Let's go!" {share} />
		{/if}
	</div>
</footer>

<style>
	.shuffle_container {
		min-width: 45%;
		align-items: baseline;
		align-content: stretch;
		flex-wrap: wrap;
		flex-direction: row;
		display: flex;
	}

	footer {
		padding-top: 35px;
		display: flex;
		width: 90%;
		justify-content: space-between;
	}

	h2 {
		font-size: 2.6em;
		font-weight: normal;
	}

	.button_container {
		margin-top: 70px;
	}

	@media only screen and (max-width: 600px) {
		h2 {
			font-size: 1em;
		}
	}
</style>
