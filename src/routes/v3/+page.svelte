<script>
   import { onMount } from "svelte";

   let LIST = [
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

   let mouseY;
   let draggingNode;
   let isDragging = false;
   let placeholderClone;
   let placeholder;
   let position;
   let from, to;
   let targetElementId;

   $: console.log(isDragging);

   function moveItem() {
      const item = LIST.splice(from, 1)[0];

      if (position) {
         LIST.splice(to, 0, item);
      }

      console.log(`From ${from} to ${to}`);
      LIST = LIST;
   }

   function draggable(node) {
      // let oldStyle = node.style;
      const placeholderRect = node.getBoundingClientRect();
      const height = placeholderRect.bottom - placeholderRect.top;
      // console.log('rect', placeholderRect);

      node.addEventListener('mousemove', (event) => {
         const thisId = LIST.indexOf(LIST.find(v => v.id == node.dataset.itemId));

         if (isDragging && from != thisId) {
            // console.log(from, to);
            // console.log(node.dataset.itemId, LIST[to].id, targetElementId);

            if (mouseY > placeholderRect.top && mouseY < placeholderRect.bottom - (height / 2)) {
               // console.log('TOP', node.dataset.itemId);
               // node.style.background = 'green';

               if (position !== 'up') {
                  // console.log(position);
                  position = 'up';
                  // console.log(event.target.dataset.itemId, node.dataset.itemId);
                  moveItem(from, to);
                  from = to;
                  // targetElementId = node.dataset.itemId;
                  // moveItemNew();
               }
            } else if (mouseY < placeholderRect.bottom && mouseY > placeholderRect.top + (height / 2)) {
               // console.log('BOTTOM', node.dataset.itemId);
               // node.style.background = 'red';

               if (position !== 'down') {
                  // console.log(position);
                  position = 'down';
                  // console.log(event.target.dataset.itemId, node.dataset.itemId);
                  moveItem(from, to);
                  from = to;
                  // targetElementId = node.dataset.itemId;
                  // moveItemNew();
               }
            }

            // console.log('INDEX', LIST.indexOf(LIST.find(v => v.id == node.dataset.itemId)));

            // if (isOver) {
            //    // console.log(node.innerText);
            // } else {
            //    node.style = oldStyle;
            // }
            
            // mouseY = event.clientY;
            // console.log(event.clientY);
         }
      })

      node.addEventListener('mousedown', () => {
         const nodeClone = node.cloneNode(true);
         placeholderClone = nodeClone.outerHTML;
         isDragging = true;
         draggingNode = node;
         // console.log(`Mouse down :: ${ node.innerText }`);
         from = LIST.indexOf(LIST.find(v => v.id == node.dataset.itemId))
         console.log(`Set from ${from}`);
         // console.log('ORIGIN INDEX', from);
      });

      node.addEventListener('mouseover', () => {
         if (isDragging && from != LIST.indexOf(LIST.find(v => v.id == node.dataset.itemId))) {
            to = LIST.indexOf(LIST.find(v => v.id == node.dataset.itemId));
            console.log(`Set to ${to}`);
            // console.log('TARGET INDEX', to);
         }
      })

      node.addEventListener('mouseleave', () => {
         position = null;
      })
   }

   onMount(() => {
      // document.addEventListener('mousedown', () => {
      //    if (placeholder) {
      //       placeholder.style = draggingNode.style;
      //       // console.log(getComputedStyle(draggingNode).width);
      //    }
      // })

      document.addEventListener('mouseup', () => {
         isDragging = false;
         // console.log(`Mouse up`);
         // console.log(`From ${from} to ${to}`);
      })

      document.addEventListener('mousemove', (event) => {
         if (isDragging) {
            mouseY = event.clientY;
            const elementHeight = parseInt(getComputedStyle(placeholder).height.match(/\d+/g)[0]);
            placeholder.style.top = `${event.clientY - elementHeight + 10}px`;
            placeholder.style.left = `${event.clientX - 60}px`;
         }
      })
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
      <div bind:this={placeholder} class="absolute pointer-events-none">
            <!-- {@html placeholderClone} -->
            <div class="rounded-lg p-6 w-[10rem] border border-dashed border-neutral-500 bg-black bg-opacity-50"></div>
      </div>
   {/if}
</div>