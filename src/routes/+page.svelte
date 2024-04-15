<script>
   import Draggy from "$lib/Draggy.svelte";
   import DraggyItem from "$lib/DraggyItem.svelte";
   import DraggyVoid from "$lib/DraggyVoid.svelte";
   import DraggyPlaceholder from "$lib/DraggyPlaceholder.svelte";

   let rocketLeagueCarsList = {
      'Meta Cars': [
         'Fennec',
         'Dominus',
         'Octane'
      ],
      'Kinda Rejected Cars': [
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

<Draggy class="flex flex-row h-[100rem]" let:list bind:list={rocketLeagueCarsList}>
   {#each list as singleList}
      <div class="relative flex flex-col w-[20rem] gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
         <h2>{ singleList.context_id }</h2>
   
            {#each singleList.list as item}   
               <DraggyItem {item}>
                  <div class="drag_me_li relative p-6 border-2 border-neutral-800 rounded-lg">
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
      <div class="w-[20rem] p-6 border-2 border-neutral-800 rounded-lg bg-black bg-opacity-75">
         <i data-draggy-grab class="ti ti-menu-2 mr-3 cursor-grab"></i>
         Operation
      </div>
   </DraggyPlaceholder>
</Draggy>