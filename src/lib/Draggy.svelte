<script>
   import { onMount, setContext } from "svelte";
   import { writable } from "svelte/store";
   
   export let list;

   let from = writable();
   let _list = writable(draggify(list));
   let isDragging = writable(false);
   let updateACC = writable(0);
   let targetItem = writable();

   setContext('from', from);
   setContext('list', _list);
   setContext('isDragging', isDragging);
   setContext('targetItem', targetItem);
   setContext('deleteItem', deleteItem);
   setContext('addItem', addItem);
   setContext('addList', addList);
   setContext('updateUI', updateUI);

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

   export function deleteItem (contextID, itemID) {
      let thisList = $_list.find(list => list.context_id === contextID).list
      // console.log(contextID, thisList.find(op => op.id === itemID));
      thisList = thisList.filter(op => op.id !== itemID);
      $_list.find(list => list.context_id === contextID).list = thisList;
      updateUI();
   }

   export function addItem (contextID, content) {
      $_list.find(list => list.context_id === contextID).list.push({
         id: `id_${Math.random().toString().slice(2,10)}`,
         context_id: contextID,
         data: content
      });
      // console.log($_list.find(list => list.context_id === contextID).list);
      console.log($_list);
      updateUI();
   }

   export function setList (updated_list) {
      console.log('[OLD]', $_list);
      $_list = draggify(updated_list);
      console.log('[NEW]', $_list);
   }

   export function addList (contextID) {
      if (!$_list.find(list => list.context_id === contextID)) {
         // console.log('Creating list with the Context ID of ', contextID);
         $_list.push({
            context_id: contextID,
            list: []
         })
         updateUI();
      } else {
         // console.log('[CREATE_LIST_FAIL] List already exists: ', $_list.find(list => list.context_id === contextID));
      }
   }

   export function deleteList (contextID) {
      $_list = $_list.filter(list => list.context_id !== contextID);
   }

   export function updateUI () {
      $updateACC++;
      $_list = $_list;
      // console.log('Update');
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

{#key $updateACC}   
   <div class={$$restProps?.class || ''} data-draggy-member="draggy_root">
      <slot update={updateUI} list={$_list} />
   </div>
{/key}