<script lang="ts">
	//ParaglideJS
	import * as m from '@src/paraglide/messages';

	import { createEventDispatcher } from 'svelte';
	import { mode, modifyEntry, handleSidebarToggle, storeListboxValue, toggleLeftSidebar, screenWidth } from '@stores/store';
	import { get } from 'svelte/store';

	const dispatch = createEventDispatcher();

	let dropdownOpen = false;
	let actionname: string;
	let buttonClass: string;
	let iconValue: string;

	function handleButtonClick() {
		if ($storeListboxValue === 'create') {
			mode.set('create');
		} else {
			mode.set('view');
			console.log('EntryListMultibutton.svelte', $mode);
			$modifyEntry($storeListboxValue);
		}

		dispatch($storeListboxValue);
		dropdownOpen = false;

		if (get(screenWidth) === 'mobile') {
			toggleLeftSidebar.clickBack();
		}

		handleSidebarToggle();
	}

	// handleOptionClick for Button Dropdown
	function handleOptionClick(value: string): void {
		storeListboxValue.set(value as 'create' | 'publish' | 'unpublish' | 'schedule' | 'clone' | 'delete' | 'test');
		dropdownOpen = false;
	}

	const buttonMap: Record<string, [string, string, string]> = {
		create: [m.entrylist_multibutton_create(), 'gradient-tertiary', 'ic:round-plus'],
		publish: [m.entrylist_multibutton_publish(), 'gradient-primary', 'bi:hand-thumbs-up-fill'],
		unpublish: [m.entrylist_multibutton_unpublish(), 'gradient-yellow', 'bi:pause-circle'],
		schedule: [m.entrylist_multibutton_schedule(), 'gradient-pink', 'bi:clock'],
		clone: [m.entrylist_multibutton_clone(), 'gradient-secondary', 'bi:clipboard-data-fill'],
		delete: [m.entrylist_multibutton_delete(), 'gradient-error', 'bi:trash3-fill'],
		test: [m.entrylist_multibutton_testing(), 'gradient-error', 'icon-park-outline:preview-open']
	};

	// a reactive statement that runs whenever storeListboxValue is updated
	$: {
		[actionname, buttonClass, iconValue] = buttonMap[$storeListboxValue] || ['', '', ''];
		buttonClass = `btn ${buttonClass} rounded-none w-36 justify-between`;
	}
</script>

<!-- Multibutton group-->
<div class="variant-filled-token rounded-0 btn-group z-10 rounded-l-full rounded-r-md font-medium text-white rtl:rounded rtl:rounded-r-full">
	<!-- left button -->
	<button type="button" class={`w-[60px] md:w-auto rtl:rotate-180 ${buttonClass}`} on:click|preventDefault={handleButtonClick}>
		<span class="flex items-center rtl:rotate-180">
			<iconify-icon icon={iconValue} width="24" class="text-white" />
			<span class="ml-2 hidden md:block rtl:mr-2">{actionname}</span>
		</span>
	</button>
	<!-- white line -->
	<div class="border-l-[3px] border-white" />

	<!-- dropdown button -->
	<!-- TODO: fix hover and roundness -->
	<button type="button" class="w-[42px] bg-surface-400 dark:bg-surface-600" on:click|preventDefault={() => (dropdownOpen = !dropdownOpen)}>
		<iconify-icon icon="mdi:chevron-down" width="24" class="text-white" />
	</button>

	{#if dropdownOpen}
		<ul class="drops absolute right-2 top-14 mt-1 divide-y divide-white rounded bg-surface-400 dark:bg-surface-700 rtl:left-2 rtl:right-auto">
			{#each Object.keys(buttonMap) as type}
				{#if $storeListboxValue !== type}
					<li class={` hover:text-white gradient-${buttonMap[type][1]}-hover gradient-${buttonMap[type][1]}-focus`}>
						<button
							type="button"
							class={`btn flex w-full justify-between gap-2 gradient-${buttonMap[type][1]} ${buttonMap[type][1]}-hover ${buttonMap[type][1]}-focus`}
							on:click|preventDefault={() => handleOptionClick(type)}
						>
							<iconify-icon icon={buttonMap[type][2]} width="24" class="" />
							<p class="">{buttonMap[type][0]}</p>
						</button>
					</li>
				{/if}
			{/each}
		</ul>
	{/if}
</div>
