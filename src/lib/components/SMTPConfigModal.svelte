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

	const inputWrapClass =
		'flex items-center w-full rounded-xl border border-gray-100 bg-gray-50 px-3.5 py-2.5 transition-colors focus-within:border-black dark:border-gray-800 dark:bg-gray-850 dark:focus-within:border-white';
	const inputClass =
		'w-full text-sm bg-transparent outline-hidden placeholder:text-gray-300 dark:placeholder:text-gray-700';
	const labelClass = 'mb-1 text-sm font-medium text-gray-700 dark:text-gray-200';

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

<Modal size="md" bind:show>
	<div>
		<div class=" flex justify-between dark:text-gray-100 px-6 pt-5 pb-2">
			<h1 class="text-xl font-medium self-center font-primary">
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

		<div class="flex flex-col w-full px-6 pb-6 dark:text-gray-200">
			<form
				class="flex flex-col w-full"
				on:submit={(e) => {
					e.preventDefault();
					submitHandler();
				}}
			>
				<div>
					<div class="text-sm text-gray-500 mb-4">
						{$i18n.t('Send emails using your own mailbox. These settings are used when sending on your behalf.')}
					</div>

					<div class="flex gap-3">
						<div class="flex flex-col w-2/3">
							<label for="smtp-host-input" class={labelClass}>{$i18n.t('Host')}</label>
							<div class={inputWrapClass}>
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
						</div>

						<div class="flex flex-col w-1/3">
							<label for="smtp-port-input" class={labelClass}>{$i18n.t('Port')}</label>
							<div class={inputWrapClass}>
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
					</div>

					<div class="flex gap-3 mt-4">
						<div class="flex flex-col w-full">
							<label for="smtp-username-input" class={labelClass}>{$i18n.t('Username')}</label>
							<div class={inputWrapClass}>
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
					</div>

					<div class="flex gap-3 mt-4">
						<div class="flex flex-col w-full">
							<label for="smtp-password-input" class={labelClass}>{$i18n.t('Password')}</label>
							<SensitiveInput
								id="smtp-password-input"
								variant="settings"
								outerClassName={inputWrapClass + ' !h-auto'}
								inputClassName="text-sm"
								bind:value={password}
								placeholder={$i18n.t('Password')}
								required={false}
							/>
						</div>
					</div>

					<div class="flex gap-3 mt-4">
						<div class="flex flex-col w-full">
							<label for="smtp-from-input" class={labelClass}>{$i18n.t('From Address')}</label>
							<div class={inputWrapClass}>
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
					</div>

					<div class="flex items-center justify-between mt-5">
						<label for="smtp-tls-toggle" class={labelClass}>{$i18n.t('Use TLS/SSL')}</label>
						<Switch id="smtp-tls-toggle" bind:state={useTLS} />
					</div>
				</div>

				<div class="flex justify-end items-center pt-6 text-sm font-medium">
					<button
						class="px-4 py-2 text-sm font-medium bg-black hover:bg-gray-900 text-white dark:bg-white dark:text-black dark:hover:bg-gray-100 transition rounded-full flex items-center gap-2 whitespace-nowrap {loading
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
