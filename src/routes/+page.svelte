<script>
   import Draggy from "$lib/Draggy.svelte";
   import DraggyItem from "$lib/DraggyItem.svelte";
    import DraggyPlaceholder from "$lib/DraggyPlaceholder.svelte";

   let LISTS = [
      {
         context_id: 'container_AX001',
         list: [
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_AX001',
               data: 'Item A'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_AX001',
               data: 'Item BB'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_AX001',
               data: 'Item CCC'
            }
         ]
      },
      {
         context_id: 'container_BX001',
         list: [
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_BX001',
               data: 'Item DDDD'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_BX001',
               data: 'Item EEEEE'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_BX001',
               data: 'Item FFFFFF'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_BX001',
               data: 'Item GGGGGGG'
            },
            {
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: 'container_BX001',
               data: 'Item HHHHHHHH'
            }
         ]
      }
   ];
</script>

<svelte:head>
   <title>Draggy</title>
</svelte:head>

<Draggy bind:list={LISTS}>
   <div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
      <h2>Context 1</h2>

         {#each LISTS[0].list as item}   
            <DraggyItem {item}>
               <div class="drag_me_li relative select-none p-6 border-2 border-neutral-700 rounded-md">
                  <i class="ti ti-menu-2 mr-3 cursor-grab"></i>
                  {item.data} :: {item.id}
               </div>
            </DraggyItem>
         {/each}
   </div>
         
   <div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
      <h2>Context 2</h2>
         {#each LISTS[1].list as item}   
            <DraggyItem {item}>
               <div class="drag_me_li relative select-none p-6 border-2 border-neutral-700 rounded-md">
                  <i class="ti ti-menu-2 mr-3 cursor-grab"></i>
                  {item.data} :: {item.id}
               </div>
            </DraggyItem>
         {/each}
   </div>

   <DraggyPlaceholder>
      <div class="rounded-lg p-6 w-[10rem] border border-dashed border-blue-500 bg-black bg-opacity-70"></div>
   </DraggyPlaceholder>
</Draggy>