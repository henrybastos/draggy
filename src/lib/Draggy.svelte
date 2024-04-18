<script>
   import { onMount, setContext } from "svelte";
   import { writable } from "svelte/store";
   
   export let list;

   let from = writable();
   let _list = writable(draggify(list));
   let isDragging = writable(false);
   let targetItem = writable();

   setContext('from', from);
   setContext('list', _list);
   setContext('isDragging', isDragging);
   setContext('targetItem', targetItem);

   $: {
      list = undraggify($_list);
   };

   function draggify (lists) {
      let newList = [];

      for (let [context, list] of Object.entries(lists)) {
         newList.push({
            context_id: context,
            list: list.map(item => ({
               id: `id_${Math.random().toString().slice(2,10)}`,
               context_id: context,
               data: item
            }))
         })
      }

      return newList;
   }

   function undraggify (lists) {
      let parsedObject = {};

      for (let [index, obj] of Object.entries(lists)) {
         parsedObject = { 
            ...parsedObject,
            [lists[index].context_id]: obj.list.map(item => item.data)
         };
      }

      return parsedObject;
   }

   export function update () {
      $_list = draggify(list);
   }
   
   onMount(() => {
      const defaultCursor = document.body.style.cursor;

      document.addEventListener('mouseup', () => {
         $isDragging = false;
         document.body.style.cursor = defaultCursor;
         document.body.style.userSelect = 'auto';
      });

      document.addEventListener('mousedown', (event) => {
         if (event.target.dataset.draggyGrab !== undefined) {
            $isDragging = true;
            document.body.style.cursor = 'grabbing';
            document.body.style.userSelect = 'none';
         }
      });
   })
</script>

<div class={$$restProps?.class || ''} data-draggy-member="draggy_root">
   <slot {update} list={$_list} />
</div>