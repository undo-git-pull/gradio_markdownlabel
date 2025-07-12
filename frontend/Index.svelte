<script context="module" lang="ts">
	export { default as BaseMarkdownRenderer } from "./shared/MarkdownRenderer.svelte";
</script>

<script lang="ts">
	import type { Gradio, SelectData, I18nFormatter } from "@gradio/utils";
	import MarkdownRenderer from "./shared/MarkdownRenderer.svelte";
	import { Block, BlockLabel, Empty } from "@gradio/atoms";
	import { TextHighlight } from "@gradio/icons";
	import { StatusTracker } from "@gradio/statustracker";
	import type { LoadingStatus } from "@gradio/statustracker";

	export let gradio: Gradio<{
		select: SelectData;
		change: never;
		clear_status: LoadingStatus;
	}>;
	export let elem_id = "";
	export let elem_classes: string[] = [];
	export let visible = true;
	export let value: {
		markdown_content: string;
		highlights: Array<{
			term: string;
			title: string;
			content: string;
			category: string;
			color: string;
		}>;
	} | null = null;
	let old_value: typeof value;
	export let show_side_panel: boolean = true;
	export let panel_width: string = "300px";
	export let label = gradio.i18n("markdown_label.markdown_label");
	export let container = true;
	export let scale: number | null = null;
	export let min_width: number | undefined = undefined;
	export let interactive: boolean = false;
	export let show_label = true;
	export let rtl = false;

	export let loading_status: LoadingStatus;

	$: {
		if (value !== old_value) {
			old_value = value;
			gradio.dispatch("change");
		}
	}
</script>

<Block
	variant={"solid"}
	test_id="markdown-label"
	{visible}
	{elem_id}
	{elem_classes}
	padding={false}
	{container}
	{scale}
	{min_width}
	{rtl}
>
	<StatusTracker
		autoscroll={gradio.autoscroll}
		i18n={gradio.i18n}
		{...loading_status}
		on:clear_status={() => gradio.dispatch("clear_status", loading_status)}
	/>
	{#if label && show_label}
		<BlockLabel
			Icon={TextHighlight}
			{label}
			float={false}
			disable={container === false}
			{show_label}
			{rtl}
		/>
	{/if}

	{#if value && value.markdown_content}
		<MarkdownRenderer
			markdown_content={value.markdown_content}
			highlights={value.highlights || []}
			{show_side_panel}
			{panel_width}
			on:select={({ detail }) => gradio.dispatch("select", detail)}
		/>
	{:else}
		<Empty>
			<TextHighlight />
		</Empty>
	{/if}
</Block>
