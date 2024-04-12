<script>
   import { onMount, getContext } from "svelte";
   // let isDragging = getContext('isDragging');
   let from = getContext('from');
   let list = getContext('list');
   let isDragging = getContext('isDragging');
   let globalList = getContext('globalList');
   let targetItem = getContext('targetItem');

   export let item;

   const thisList = $list.find(v => v.context_id == item.context_id).list;
   let to;
   let targetList;
   let position;
   let mouseY;

   // $: console.log($from, to);

   function moveItem() {
      // Delete item from its origin list
      // console.log($list.find(v => v.context_id == $targetItem.context_id).list, $from);
      const targetlist = $list.find(v => v.context_id == $targetItem.context_id).list;
      
      $list.find(v => v.context_id == $targetItem.context_id).list.splice($from, 1);

      console.log($list[0].list[0]);
      console.log($list[0].list[1]);
      
      // if (targetlist.find(_item => _item.context_id === $targetItem.context_id)) {
      //    $list.find(v => v.context_id == $targetItem.context_id).list.splice($from, 1);
      //    console.log(targetlist.find(_item => _item.context_id === $targetItem.context_id));
      // }

      if (position) {
         // Add item to the targeted list
         targetList.list.splice(to, 0, $targetItem);
      }

      // To update the list
      $list = $list;
      // console.log($list);
      // console.log(`From ${$from} to ${to}`);
   }

   function draggable(node) {
      node.addEventListener('mousemove', () => {
         const index = thisList.indexOf(thisList.find(v => v.id == node.dataset.itemId));
         
         if ($isDragging && $from != index) {
            const nodeRect = node.getBoundingClientRect();
            const height = nodeRect.bottom - nodeRect.top;

            if (mouseY > nodeRect.top && mouseY < nodeRect.bottom - (height / 2)) {
               if (position !== 'up') {
                  position = 'up';
                  moveItem();
                  $from = to;
                  // console.log('FROM TO', $from, to);
               }
            } else if (mouseY < nodeRect.bottom && mouseY > nodeRect.top + (height / 2)) {
               if (position !== 'down') {
                  position = 'down';
                  moveItem();
                  $from = to;
                  // console.log('FROM TO', $from, to);
               }
            }

         }
      })

      node.addEventListener('mousedown', () => {
         const index = thisList.indexOf(thisList.find(_item => _item.id == node.dataset.itemId));
         // const nodeClone = node.cloneNode(true);
         
         $from = index;
         $targetItem = thisList[$from];
         // console.log(`Set $from ${$from}`);
      });

      node.addEventListener('mouseover', () => {
         const index = thisList.indexOf(thisList.find(v => v.id == node.dataset.itemId));

         node.style.background = '#222';
         
         if ($isDragging && $from != index) {
            to = index;
            targetList = $list.find(v => v.context_id == node.dataset.itemContextId);
            // console.log(`Set to ${to}`);
         }
      })

      node.addEventListener('mouseleave', () => {
         node.style.background = 'transparent';
         position = null;
      })
   }

   onMount(() => {
      document.addEventListener('mousemove', (event) => {
         if ($isDragging) {
            mouseY = event.clientY;
         }
      })
   })
</script>

<div use:draggable data-item-id={item.id} data-item-context-id={item.context_id}>
   <slot />
</div>