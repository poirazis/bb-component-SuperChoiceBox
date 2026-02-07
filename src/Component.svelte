<script lang="ts">
  import { getContext } from "svelte";

  const { styleable, Provider, ContextScopes, builderStore } =
    getContext("sdk");
  const component = getContext("component");

  let {
    optionsSource,
    dataProvider,
    valueField,
    labelField,
    orientation,
    columns,
    options,
    staticOptions,
    onChange,
    disabled,
    value = "",
    selectedBgColor = "",
    selectedColor = "",
    children,
  }: {
    optionsSource?: string;
    dataProvider?: any;
    valueField?: string;
    labelField?: string;
    orientation?: string;
    columns?: number;
    options?: string;
    staticOptions?: [];
    value?: string;
    disabled?: boolean;
    onChange?: (value: string) => void;
    children?: any;
  } = $props();

  let optionList = $derived(
    optionsSource == "custom" && options
      ? options
          .split(",")
          .map((s) => s.trim())
          .map((v) => ({ value: v, label: v, row: null }))
      : optionsSource == "data" && dataProvider
        ? dataProvider?.rows?.map((row) => ({
            row: row,
            value: valueField ? row[valueField] : "No value Field Selected",
            label:
              valueField && labelField
                ? row[labelField]
                : valueField
                  ? row[valueField]
                  : "No value Field Selected",
          }))
        : optionsSource == "static" && staticOptions?.length
          ? staticOptions.map((opt) => ({
              ...opt,
              row: null,
            }))
          : [],
  );

  let hasChildren = $derived($component.children > 0);

  $effect(() => {
    if (!(value && optionList.some((o) => o.value === value))) {
      value = optionList[0]?.value || "";
    }
  });
</script>

<Provider data={{ value }} />
<div
  class="setter"
  style:--selected-bg-color={selectedBgColor
    ? selectedBgColor
    : "var(--spectrum-global-color-gray-200)"}
  style:--selected-color={selectedColor
    ? selectedColor
    : "var(--spectrum-global-color-gray-800)"}
  style={orientation === "grid" && columns
    ? `--columns: ${columns}`
    : undefined}
>
  {#if optionList.length < 1 && $builderStore.inBuilder}
    <div class="super-wrapper empty" use:styleable={$component.styles}>
      No options found. Please add options or connect a data source.
    </div>
  {:else}
    <div
      class="super-wrapper"
      class:vertical={orientation === "vertical"}
      class:horizontal={orientation === "horizontal"}
      class:grid={orientation === "grid"}
    >
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      {#each optionList as option}
        <!-- svelte-ignore a11y_click_events_have_key_events -->
        <!-- svelte-ignore event_directive_deprecated -->
        <div
          class="option"
          class:selected={value == option.value}
          use:styleable={$component.styles}
          on:click={() => {
            if (disabled) return;
            value = option.value;
            onChange?.({ value });
          }}
        >
          <div class="title">
            <span>{option.label}</span>

            {#if value == option.value}
              <i class="ph ph-check-circle"></i>
            {:else}
              <i class="ph ph-circle"></i>
            {/if}
          </div>
          {#if hasChildren}
            <Provider
              data={{ value, optionValue: option.value, optionRow: option.row }}
              scope={ContextScopes.Local}
            >
              <div class="contents">
                {@render children()}
              </div>
            </Provider>
          {/if}
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  .super-wrapper {
    display: flex;
    flex-direction: column;
    align-items: stretch;
    gap: 0.5rem;
    overflow: hidden;
  }

  .super-wrapper.empty {
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--spectrum-global-color-gray-500);
    font-style: italic;
    border: 1px dashed var(--spectrum-global-color-gray-500);
    padding: 1rem;
  }

  .super-wrapper.horizontal {
    flex-direction: row;
    overflow: hidden;
    min-width: 0;
  }

  .super-wrapper.grid {
    display: grid;
    grid-template-columns: repeat(var(--columns, auto-fit), minmax(150px, 1fr));
    gap: 0.5rem;
  }

  .setter {
    display: contents;
  }

  .super-wrapper .option {
    flex: 1;
    padding: 1rem;
    cursor: pointer;
    border-radius: 8px;
    border: 1px solid var(--spectrum-global-color-gray-200);
    background-color: color-mix(
      in srgb,
      var(--spectrum-global-color-gray-50) 50%,
      transparent
    );
    display: flex;
    flex-direction: column;
    align-items: stretch;
    gap: 1rem;
    transition:
      background 0.2s,
      border-color 0.2s;
    min-width: 0;
  }

  .super-wrapper .option:hover {
    background-color: color-mix(
      in srgb,
      var(--selected-bg-color, --spectrum-global-color-gray-200) 50%,
      transparent
    ) !important;
  }

  .option:hover .ph-circle {
    color: var(--spectrum-global-color-gray-500);
  }

  .super-wrapper .option:focus {
    outline: 2px solid var(--accent-color);
    outline-offset: 2px;
  }

  .super-wrapper .option:disabled {
    background-color: var(--spectrum-global-color-gray-200);
    color: var(--spectrum-global-color-gray-500);
    cursor: not-allowed;
  }

  .super-wrapper .option.selected {
    border: 1px solid var(--selected-bg-color --spectrum-global-color-gray-200);
    background-color: var(
      --selected-bg-color,
      --spectrum-global-color-gray-200
    ) !important;
  }

  .option .title {
    display: flex;
    justify-content: space-between;
    font-weight: 500;
    color: var(--spectrum-global-color-gray-700);
    overflow: hidden;
    gap: 1.5rem;
    min-width: 0;
  }

  .option .title span {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
  }

  .option.selected .title {
    color: var(--selected-color, --spectrum-global-color-gray-800);
    font-weight: 600;
  }

  .option .title .ph-check-circle {
    display: none;
  }

  .option.selected .title .ph-check-circle {
    display: block;
    color: var(--selected-color, --spectrum-global-color-green-700);
    font-size: 18px;
  }

  .option .contents {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .ph-circle {
    color: var(--spectrum-global-color-gray-300);
    font-size: 18px;
  }
</style>
