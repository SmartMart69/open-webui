<script lang="ts">
	import { getVersionUpdates } from '$lib/apis';
	import { getOllamaVersion } from '$lib/apis/ollama';
	import { WEBUI_BUILD_HASH, WEBUI_VERSION } from '$lib/constants';
	import { WEBUI_NAME, config, showChangelog } from '$lib/stores';
	import { compareVersion } from '$lib/utils';
	import { onMount, getContext } from 'svelte';

	const i18n = getContext('i18n');
</script>

<div class="flex flex-col h-full justify-between space-y-3 text-sm mb-6">
	<div class=" space-y-3 overflow-y-scroll max-h-[28rem] lg:max-h-full">
		<div>
			<div class=" mb-2.5 text-sm font-medium flex space-x-2 items-center">
				<div>
					{$WEBUI_NAME}
					{$i18n.t('Version')} 6
				</div>
			</div>
			
		</div>

		<hr class=" border-gray-100 dark:border-gray-850" />

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
			Emoji graphics provided by
			<a href="https://github.com/jdecked/twemoji" target="_blank">Twemoji</a>, licensed under
			<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">CC-BY 4.0</a>.
		</div>

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
				<span class=" text-gray-500 dark:text-gray-300 font-medium">{$WEBUI_NAME}</span> -
		
			Operated by
			<a
				class=" text-gray-500 dark:text-gray-300 font-medium"
				href="https://chatgurus.de"
				target="_blank"> ChatGurus</a
			>
		</div>
	</div>
</div>
