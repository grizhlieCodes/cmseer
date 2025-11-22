<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { type BUTTON_STATE } from '$lib/schemas-and-types/types';
	import {
		type ButtonVariant,
		type ButtonProps,
		type ButtonSize
	} from '$lib/components/ui/button/button.svelte';

	type Props = {
		children: any;
		state?: BUTTON_STATE;
		variant?: ButtonVariant;
		size?: ButtonSize;
	};

	let { children, state = 'default', variant = 'default', size = 'default' }: Props = $props();
</script>

<Button {variant} {size}>
	{#if state === 'default'}
		{@render children?.()}
    {:else if state === 'submitting'}
        submitting
    {:else if state === 'success'}
        Yay success!
    {:else if state === 'error'}
        noo, error!
	{/if}
</Button>
