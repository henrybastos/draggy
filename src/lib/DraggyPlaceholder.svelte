<script>
    import { onMount, getContext } from "svelte";

   let placeholder;
   let mouseY;

   let isDragging = getContext('isDragging');

   onMount(() => {
      document.addEventListener('mousemove', (event) => {
         if ($isDragging) {
            const elementHeight = parseInt(getComputedStyle(placeholder).height.match(/\d+/g)[0]);
            mouseY = event.clientY;
            placeholder.style.top = `${event.clientY - elementHeight + 10}px`;
            placeholder.style.left = `${event.clientX - 60}px`;
         }
      })
   })
</script>

{#if $isDragging}
   <div bind:this={placeholder} class="absolute pointer-events-none">
         <!-- {@html placeholderClone} -->
         <!-- <div class="rounded-lg p-6 w-[10rem] border border-dashed border-neutral-500 bg-black bg-opacity-50"></div> -->
         <slot />
   </div>
{/if}