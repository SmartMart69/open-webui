<script lang="ts">
	import { onMount, getContext } from 'svelte';
	import { toast } from 'svelte-sonner';
	import { settings } from '$lib/stores';
	import { updateUserSettings } from '$lib/apis/users';
	import { addNewMemory } from '$lib/apis/memories';
	import Modal from '../common/Modal.svelte';
	
	const i18n = getContext('i18n');

	// Steuert, ob der Dialog angezeigt wird
	export let show = false;

	// System Prompt
	let system = '';

	// Zustand für den Memory-Bereich
	let memoryContent = '';
	let memoryLoading = false;
	let enableMemory = true;

	// Beim Mounten den aktuellen System Prompt laden
	onMount(() => {
		system = $settings.system ?? '';
		enableMemory = true;
	});

	// Kombiniert das Hinzufügen eines Memorys (falls Inhalt vorhanden) und das Speichern des System Prompts
	const handleSaveAll = async () => {
  // Beide Felder müssen etwas enthalten!
  if (system.trim() === "" || memoryContent.trim() === "") {
    toast.error($i18n.t('Please fill in all fields.'));
    return; // Abbruch, falls eines der Felder leer ist
  }

  // Memory hinzufügen
  memoryLoading = true;
  const res = await addNewMemory(localStorage.token, memoryContent).catch((error) => {
    toast.error(`${error}`);
    return null;
  });
  if (res) {
    toast.success($i18n.t('Memory added successfully'));
    memoryContent = ''; // Inhalt zurücksetzen
  }
  memoryLoading = false;

  // System Prompt speichern
  await settings.set({ ...$settings, system, memory: enableMemory });
  await updateUserSettings(localStorage.token, { ui: { system, memory: enableMemory } });
  toast.success($i18n.t('Settings saved successfully!'));


  // Dialog schließen
  show = false;
};
</script>

<Modal size="xl" bind:show closeOnOutsideClick={false}>
	<div class="text-gray-700 dark:text-gray-100">
		<!-- Header -->
		<div class="flex justify-between dark:text-gray-300 px-5 pt-4 pb-1">
			<div class="text-lg font-medium self-center">
				{$i18n.t('Please personalize the system')}
			</div>
			<!-- Kein separater Schließen-Button -->
		</div>

		<div class="px-4 pt-1 pb-4 space-y-6">
			<!-- System Prompt Section -->
			<div>
				<div class="my-2.5 text-sm font-medium">
					{$i18n.t('System Instructions')}
				</div>
				<textarea
					bind:value={system}
					class="w-full rounded-lg p-4 text-sm dark:text-gray-300 dark:bg-gray-850 outline-none resize-none"
					rows="4"
					placeholder={$i18n.t('Enter your assitants role or task')}
				/>
				<div class="text-xs text-gray-500 mt-1">
					ⓘ {$i18n.t('Give the assistant a basic instruction about his role (e.g., "You are an expert for digital marketing")')}
				</div>
			</div>

			<!-- Memory Section -->
			<div>
				<div class="my-2.5 text-sm font-medium">
					{$i18n.t('Add Memory')}
				</div>
				<textarea
					bind:value={memoryContent}
					class="bg-transparent w-full text-sm resize-none rounded-xl p-3 outline outline-1 outline-gray-100 dark:outline-gray-800"
					rows="3"
					placeholder={$i18n.t('Enter a detail about yourself for your LLMs to recall')}
				/>
				<div class="text-xs text-gray-500 mt-1">
					ⓘ {$i18n.t('Refer to yourself as "User" (e.g., "User is learning Spanish")')}
				</div>
			</div>

			<!-- Kombinierter Save-Button -->
			<div class="flex justify-end text-sm font-medium">
				<button
					on:click={handleSaveAll}
					class="px-3.5 py-1.5 text-sm font-medium bg-black hover:bg-gray-900 text-white dark:bg-white dark:text-black dark:hover:bg-gray-100 transition rounded-full flex items-center"
					disabled={memoryLoading}
				>
					{$i18n.t('Save')}
					{#if memoryLoading}
						<div class="ml-2">
							<svg class="w-4 h-4" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
								<style>
									.spinner {
										transform-origin: center;
										animation: spin 0.75s infinite linear;
									}
									@keyframes spin {
										100% { transform: rotate(360deg); }
									}
								</style>
								<path d="M12,1A11,11,0,1,0,23,12,11,11,0,0,0,12,1Zm0,19a8,8,0,1,1,8-8A8,8,0,0,1,12,20Z" opacity=".25"/>
								<path class="spinner" d="M10.14,1.16a11,11,0,0,0-9,8.92A1.59,1.59,0,0,0,2.46,12,1.52,1.52,0,0,0,4.11,10.7a8,8,0,0,1,6.66-6.61A1.42,1.42,0,0,0,12,2.69h0A1.57,1.57,0,0,0,10.14,1.16Z"/>
							</svg>
						</div>
					{/if}
				</button>
			</div>
		</div>
	</div>
</Modal>

<style>
	textarea {
		min-height: 100px;
	}
</style>
