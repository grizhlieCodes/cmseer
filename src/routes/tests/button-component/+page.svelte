<script lang="ts">
    import { Spring } from 'svelte/motion';
    import { fade } from 'svelte/transition';

    const buttonStates = ['default', 'submitting', 'success', 'error'];
    let index = $state(0);

    const btn = $derived({ variant: 'outline', state: buttonStates[index] });

    const textMap: Record<string, string> = {
        default: 'Sign In',
        submitting: 'Submitting...',
        success: 'Yay success!',
        error: 'Oh no, error!'
    };

    let currentText = $derived(textMap[btn.state]);
    
    let measuredWidth = $state(0);
    let isCalculated = $state(false);

    // 1. Create the Spring
    const widthSpring = new Spring(0, {
        stiffness: 0.15,
        damping: 0.5
    });

    $effect(() => {
        if (measuredWidth > 0) {
            // 2. LOGIC: If this is the first load, SNAP using .set()
            if (!isCalculated) {
                // .set() updates the value instantly, bypassing physics
                widthSpring.set(measuredWidth); 
                isCalculated = true; 
            } else {
                // .target updates the value with physics
                widthSpring.target = measuredWidth;
            }
        }
    });
</script>

<div class="flex min-h-screen flex-col items-center justify-center gap-10">
    <button onclick={() => (index = index === 3 ? 0 : index + 1)}>
        Next: {index}
    </button>

    <button 
        class="premium-btn" 
        style:width={isCalculated ? `${widthSpring.current}px` : 'auto'}
    >
        <span class="ghost-measurer" aria-hidden="true" bind:clientWidth={measuredWidth}>
            {currentText}
        </span>

        <div class="content-stage">
            {#key currentText}
                <span 
                    class="text-animator"
                    in:fade={{ duration: 200, delay: 150 }} 
                    out:fade={{ duration: 150 }}
                >
                    {currentText}
                </span>
            {/key}
        </div>
    </button>
</div>

<style>
    .premium-btn {
        display: inline-flex;
        overflow: hidden;
        position: relative; 
        height: 44px; 
        background: #fff;
        color: #000;
        border: 1px solid #ccc;
        border-radius: 6px;
        font-weight: 600;
        white-space: nowrap;
        align-items: center;
        justify-content: center;
        /* Add transition for background color so it matches the premium feel */
        transition: background-color 0.2s, border-color 0.2s; 
    }

    .ghost-measurer {
        visibility: hidden;
        padding: 0 1.5rem;
        opacity: 0;
        pointer-events: none;
        white-space: nowrap;
        display: block; 
        height: 100%; 
    }

    .content-stage {
        position: absolute;
        inset: 0;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    
    .text-animator {
        position: absolute;
    }
</style>