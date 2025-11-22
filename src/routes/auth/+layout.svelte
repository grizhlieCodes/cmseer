<script lang="ts">
	let { children } = $props();
</script>

<div class="min-h-screen grid place-items-center">
    {@render children?.()}
</div>
