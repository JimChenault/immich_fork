<script lang="ts">
  import Icon from '$lib/components/elements/icon.svelte';
  import { savedSearchTerms } from '$lib/stores/search.store';
  import { mdiMagnify, mdiClose } from '@mdi/js';
  import { fly } from 'svelte/transition';
  import { t } from 'svelte-i18n';
  import CircleIconButton from '$lib/components/elements/buttons/circle-icon-button.svelte';

  export let searchQuery: string = '';
  export let isSearchSuggestions: boolean = false;
  export let onSelectSearchTerm: (searchTerm: string) => void;
  export let onClearSearchTerm: (searchTerm: string) => void;
  export let onClearAllSearchTerms: () => void;

  $: filteredSearchTerms = $savedSearchTerms.filter((term) => term.toLowerCase().includes(searchQuery.toLowerCase()));
  $: isSearchSuggestions = filteredSearchTerms.length > 0;
</script>

<div transition:fly={{ y: 25, duration: 250 }}>
  {#if isSearchSuggestions}
    <div
      class="absolute w-full rounded-b-3xl border-2 border-t-0 border-gray-200 bg-white pb-5 shadow-2xl transition-all dark:border-gray-600 dark:bg-immich-dark-gray dark:text-gray-300"
      role="listbox"
    >
      <div class="flex items-center justify-between px-5 pt-5 text-xs">
        <p>{$t('recent_searches').toUpperCase()}</p>
        <button
          type="button"
          class="rounded-lg p-2 font-semibold text-immich-primary hover:bg-immich-primary/25 dark:text-immich-dark-primary"
          on:click={() => onClearAllSearchTerms()}
          tabindex="-1"
        >
          {$t('clear_all')}
        </button>
      </div>

      {#each filteredSearchTerms as savedSearchTerm, i (i)}
        <div
          class="flex w-full items-center justify-between text-sm text-black hover:bg-gray-100 dark:text-gray-300 dark:hover:bg-gray-500/10"
        >
          <div class="relative w-full items-center">
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <div
              class="relative flex w-full cursor-pointer gap-3 py-3 pl-5"
              on:click={() => onSelectSearchTerm(savedSearchTerm)}
              role="option"
              tabindex="-1"
              aria-selected="false"
            >
              <Icon path={mdiMagnify} size="1.5em" ariaHidden={true} />
              {savedSearchTerm}
            </div>
            <div class="absolute right-5 top-0 items-center justify-center py-3">
              <CircleIconButton
                icon={mdiClose}
                title={$t('remove')}
                size="18"
                padding="1"
                tabindex={-1}
                on:click={() => onClearSearchTerm(savedSearchTerm)}
              />
            </div>
          </div>
        </div>
      {/each}
    </div>
  {/if}
</div>
