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
   $: list = $_list;
   
   onMount(() => {
      document.addEventListener('mouseup', () => {
         $isDragging = false;
      });

      document.addEventListener('mousedown', () => {
         $isDragging = true;
      });
   })
</script>

<pre>{JSON.stringify(list, null, 3)}</pre>

<slot />