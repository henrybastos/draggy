<script>
   import Draggy from "$lib/Draggy.svelte";
   import DraggyItem from "$lib/DraggyItem.svelte";
   import DraggyVoid from "$lib/DraggyVoid.svelte";
   import DraggyPlaceholder from "$lib/DraggyPlaceholder.svelte";

   let rocketLeagueCarsList = {
      meta: [
         'Fennec',
         'Dominus',
         'Octane'
      ],
      bleh: [
         'Animus GP',
         'Endo',
         'Scarab',
         'Zippy',
         'Grog',
         'Roadhog'
      ]
   };
</script>

<svelte:head>
   <title>Draggy</title>
</svelte:head>

<Draggy let:list bind:list={rocketLeagueCarsList}>
   {#each list as singleList}
      <div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
         <h2>{ singleList.context_id }</h2>
   
            {#each singleList.list as item}   
               <DraggyItem {item}>
                  <div class="drag_me_li relative select-none p-6 border-2 border-neutral-800 rounded-lg">
                     <i data-draggy-grab class="ti ti-menu-2 mr-3 cursor-grab"></i>
                     {item.data}
                  </div>
               </DraggyItem>
            {:else}
               <DraggyVoid contextID={singleList.context_id} class="">
                  <p class="w-full text-center text-neutral-500 font-semibold">No items found</p>
               </DraggyVoid>
            {/each}
      </div>
   {/each}

   <DraggyPlaceholder offset={{ x: -26 }}>
      <div class="relative w-[20rem] select-none p-6 border-2 border-neutral-800 rounded-lg bg-black bg-opacity-75">
         <i data-draggy-grab class="ti ti-menu-2 mr-3 cursor-grab"></i>
         Operation
      </div>
   </DraggyPlaceholder>
</Draggy>