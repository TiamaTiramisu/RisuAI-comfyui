<script lang="ts">
    import { ColorSchemeTypeStore } from "src/ts/gui/colorscheme";
    import { ParseMarkdown } from "src/ts/parser.svelte";
    import { parseMultilangString, toLangName } from "src/ts/util";

    interface Props {
        value: string;
        markdown?: boolean;
    }

    let { value, markdown = false }: Props = $props();
    const parsedValue = parseMultilangString(value)
    let valueObject: {[code:string]:string} = $state(parsedValue)
    let selectedLang = $state("en")
    if(parsedValue["en"] === undefined){
        selectedLang = "xx"
    }
    $effect.pre(() => {
        valueObject = parseMultilangString(value)
    });
</script>

<div class="flex flex-col">
    <div class="flex flex-wrap max-w-fit p-1 gap-2">
        {#each Object.keys(valueObject) as lang}
            {#if lang !== 'xx' || Object.keys(valueObject).length === 1}
                <button class="bg-bgcolor py-2 rounded-lg px-4" class:ring-1={selectedLang === lang} onclick={((e) => {
                    e.stopPropagation()
                    selectedLang = lang
                })}>{toLangName(lang)}</button>
            {/if}
        {/each}
    </div>
    {#if markdown}
        <div class="ml-2 max-w-full break-words text chat chattext prose" class:prose-invert={$ColorSchemeTypeStore}>
            {#await ParseMarkdown(valueObject[selectedLang]) then md} 
                {@html md}
            {/await}
        </div>
    {:else}
        <div class="ml-2 max-w-full break-words text chat chattext prose" class:prose-invert={$ColorSchemeTypeStore}>
            {valueObject[selectedLang]}
        </div>
    {/if}
</div>