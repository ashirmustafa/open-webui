<script lang="ts">
	import { toast } from 'svelte-sonner';
	import { getContext } from 'svelte';
	const i18n = getContext('i18n');

	import Modal from '$lib/components/common/Modal.svelte';
	import SensitiveInput from '$lib/components/common/SensitiveInput.svelte';
	import Switch from '$lib/components/common/Switch.svelte';
	import Spinner from '$lib/components/common/Spinner.svelte';
	import XMark from '$lib/components/icons/XMark.svelte';

	// UI only for now — wiring this up to a backend/n8n is a follow-up once the
	// storage/send logic is decided. Submitting just closes the modal.
	export let onSubmit: Function = () => {};

	export let show = false;

	let host = '';
	let port = '';
	let username = '';
	let password = '';
	let fromAddress = '';
	let useTLS = true;

	let loading = false;

	const inputClass =
		'w-full text-sm bg-transparent outline-hidden placeholder:text-gray-300 dark:placeholder:text-gray-700';

	const resetForm = () => {
		host = '';
		port = '';
		username = '';
		password = '';
		fromAddress = '';
		useTLS = true;
	};

	const submitHandler = async () => {
		if (!host) {
			toast.error($i18n.t('Host is required'));
			return;
		}

		if (!username) {
			toast.error($i18n.t('Username is required'));
			return;
		}

		loading = true;

		await onSubmit({
			host,
			port,
			username,
			password,
			from_address: fromAddress,
			use_tls: useTLS
		});

		loading = false;
		show = false;

		resetForm();
	};
</script>

<Modal size="sm" bind:show>
	<div>
		<div class=" flex justify-between dark:text-gray-100 px-5 pt-4 pb-1.5">
			<h1 class="text-lg font-medium self-center font-primary">
				{$i18n.t('SMTP Configuration')}
			</h1>
			<button
				class="self-center"
				aria-label={$i18n.t('Close modal')}
				on:click={() => {
					show = false;
				}}
			>
				<XMark className={'size-5'} />
			</button>
		</div>

		<div class="flex flex-col w-full px-4 pb-4 dark:text-gray-200">
			<form
				class="flex flex-col w-full"
				on:submit={(e) => {
					e.preventDefault();
					submitHandler();
				}}
			>
				<div class="px-1">
					<div class="text-xs text-gray-500 mb-2">
						{$i18n.t('Send emails using your own mailbox. These settings are used when sending on your behalf.')}
					</div>

					<div class="flex gap-2 mt-1.5">
						<div class="flex flex-col w-2/3">
							<label for="smtp-host-input" class="mb-0.5 text-xs text-gray-500"
								>{$i18n.t('Host')}</label
							>
							<input
								id="smtp-host-input"
								class={inputClass}
								type="text"
								bind:value={host}
								placeholder={$i18n.t('smtp.example.com')}
								autocomplete="off"
								required
							/>
						</div>

						<div class="flex flex-col w-1/3">
							<label for="smtp-port-input" class="mb-0.5 text-xs text-gray-500"
								>{$i18n.t('Port')}</label
							>
							<input
								id="smtp-port-input"
								class={inputClass}
								type="text"
								inputmode="numeric"
								bind:value={port}
								placeholder="587"
								autocomplete="off"
							/>
						</div>
					</div>

					<div class="flex gap-2 mt-2">
						<div class="flex flex-col w-full">
							<label for="smtp-username-input" class="mb-0.5 text-xs text-gray-500"
								>{$i18n.t('Username')}</label
							>
							<input
								id="smtp-username-input"
								class={inputClass}
								type="text"
								bind:value={username}
								placeholder={$i18n.t('you@example.com')}
								autocomplete="off"
								required
							/>
						</div>
					</div>

					<div class="flex gap-2 mt-2">
						<div class="flex flex-col w-full">
							<label for="smtp-password-input" class="mb-0.5 text-xs text-gray-500"
								>{$i18n.t('Password')}</label
							>
							<SensitiveInput
								id="smtp-password-input"
								bind:value={password}
								placeholder={$i18n.t('Password')}
								required={false}
							/>
						</div>
					</div>

					<div class="flex gap-2 mt-2">
						<div class="flex flex-col w-full">
							<label for="smtp-from-input" class="mb-0.5 text-xs text-gray-500"
								>{$i18n.t('From Address')}</label
							>
							<input
								id="smtp-from-input"
								class={inputClass}
								type="text"
								bind:value={fromAddress}
								placeholder={$i18n.t('you@example.com')}
								autocomplete="off"
							/>
						</div>
					</div>

					<div class="flex items-center justify-between mt-3">
						<label for="smtp-tls-toggle" class="text-xs text-gray-500"
							>{$i18n.t('Use TLS/SSL')}</label
						>
						<Switch id="smtp-tls-toggle" bind:state={useTLS} />
					</div>
				</div>

				<div class="flex justify-end items-center pt-3 text-sm font-medium">
					<button
						class="px-3.5 py-1.5 text-sm font-medium bg-black hover:bg-gray-900 text-white dark:bg-white dark:text-black dark:hover:bg-gray-100 transition rounded-full flex items-center gap-2 whitespace-nowrap {loading
							? ' cursor-not-allowed'
							: ''}"
						type="submit"
						disabled={loading}
					>
						{$i18n.t('Save')}

						{#if loading}
							<span class="shrink-0">
								<Spinner />
							</span>
						{/if}
					</button>
				</div>
			</form>
		</div>
	</div>
</Modal>
