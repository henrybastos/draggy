<script>
   import { onMount } from "svelte";

   const LIST = [
      {
         id: `id_${Math.random().toString().slice(2,10)}`,
         context: 'container_01',
         data: 'Item 01'
      },
      {
         id: `id_${Math.random().toString().slice(2,10)}`,
         context: 'container_01',
         data: 'Item 02'
      },
      {
         id: `id_${Math.random().toString().slice(2,10)}`,
         context: 'container_01',
         data: 'Item 03'
      }
   ];

   let draggingNode;
   let isDragging = false;
   let mouseY;

   function draggable(node) {
      const ogStyle = node.style;

      node.addEventListener('mousemove', (event) => {
         if (isDragging) {
            // mouseY = event.clientY;
            console.log(event.clientY);
         }
      })

      node.addEventListener('mousedown', () => {
         isDragging = true;
         draggingNode = node;
         // draggingNodeTop = node.getBoundingClientRect().top;
         console.log(`Mouse down :: ${ node.innerText }`);
         // console.log(getComputedStyle(node));
         // console.log();
      });

      node.addEventListener('mouseover', () => {
         if (isDragging) {
            console.log(mouseY);
            node.style.background = 'red';
         }
      })

      node.addEventListener('mouseleave', () => {
         node.style = ogStyle;
      })
   }

   onMount(() => {
      document.addEventListener('mouseup', () => {
         isDragging = false;
         console.log(`Mouse up`);
      })

      // document.addEventListener('mousemove', (event) => {
      //    if (isDragging) {
      //       mouseY = event.clientY;
      //    }
      // })
   })
</script>

<svelte:head>
   <title>V2</title>
</svelte:head>

<div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
   <!-- svelte-ignore a11y-no-static-element-interactions -->
   {#each LIST as item}
      <div
         use:draggable
         data-item-id={item.id}
         class="drag_me_li relative select-none p-6 border-2 border-neutral-700 rounded-md"
      >
         <i class="ti ti-menu-2 mr-3 cursor-grab"></i>
         {item.data} :: {item.id}
      </div>
   {/each}

   {#if isDragging}
      <div class="absolute p-6 blur-lg rounded-md border border-blue-500"></div>
   {/if}
</div>