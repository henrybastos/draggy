<script>
   import { onMount, setContext } from "svelte";
   import { writable } from "svelte/store";
   
   export let list;

   let from = writable();
   let _list = writable(list);
   let isDragging = writable(false);
   let targetItem = writable();

   setContext('from', from);
   setContext('list', _list);
   setContext('isDragging', isDragging);
   setContext('targetItem', targetItem);

   // $: console.log('LIST', list);
   // $: console.log('$_LIST', $_list);
   $: {
      list = $_list
      // for (let sublist of list) {
      //    let uniqueIDs = Array.from(new Set(sublist.list.map(v => v.id)));
      //    uniqueIDs = uniqueIDs.map(id => sublist.list.find(item => item.id === id));
      //    sublist.list = uniqueIDs;
      //    console.log(sublist);
      //    // list = [
      //    //    ...sublist
      //    // ];
      // }
      // console.log(list);
   };
   
   onMount(() => {
      document.addEventListener('mouseup', () => {
         $isDragging = false;
      });

      document.addEventListener('mousedown', (event) => {
         if (event.target.dataset.draggyGrab !== undefined) {
            $isDragging = true;
         }
      });
   })
</script>

<!-- <pre>{JSON.stringify(list, null, 3)}</pre> -->

<slot />