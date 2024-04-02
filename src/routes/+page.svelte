<script>
   import { onMount } from "svelte";
   import { slide } from 'svelte/transition'

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
   // let observer;
   let isDragging = false;
   let currentItemID, targetItemID;
   let aboveTarget;
   let currentSlotID;
   let placeholderItem;
   let placeholderClone;

   function observeEntry(node) {
      // if (observer) {
      //    observer.observe(node);
      // }
      const { top, bottom } = node.getBoundingClientRect();
      console.log(top, bottom, node.innerText);
      
      // node.addEventListener('mousemove', (event) => {
      //    // console.log('Over!');
      //    console.log(top, bottom, event.clientY);
      // })

      // node.addEventListener('mouseover', (event) => {
      //    currentSlotID = `${node.dataset.slotOrder}_${node.parentNode.dataset.itemId}`;
      //    console.log('Over!', currentSlotID);
      // });
   }

   function handleMouseMovement(event) {
      if (isDragging && placeholderItem) {
         const placeholderRect = placeholderItem.getBoundingClientRect();
         const elementHeight = parseInt(getComputedStyle(placeholderItem).height.match(/\d+/g)[0]);
         // console.log(placeholderRect.top, placeholderRect.bottom);
         checkBoundings(event.clientY);
         
         placeholderItem.style.top = `${event.clientY - elementHeight}px`;
         // placeholderItem.style.left = `${event.clientX - 50}px`;
         // console.log(top, bottom, elementHeight);
         // console.log(event.clientY, elementHeight);
      }
   }

   function handleMouseUp() {
      isDragging = false;
   }

   /**
    * Adds an event listener to a DOM node to enable dragging functionality.
    * @param {HTMLElement} node - The DOM node to which the event listener will be added.
    */
   function dragItem(node) {
      node.addEventListener("mousedown", (event) => {
         const nodeClone = node.cloneNode(true);
         isDragging = true;
         // placeholderItem = nodeClone;
         placeholderClone = nodeClone.outerHTML;
         
         // placeholderItem.appendChild(nodeClone);
         // placeholderItem.outerHtml = nodeClone.outerHtml;
         // placeholderItem = nodeClone;
         console.log(nodeClone);

         currentItemID = event.target.dataset.itemId;
         console.log(`CURRENT ID :: DOWN`, currentItemID);
      });

      node.addEventListener('mouseleave', (event) => {
         // console.log('Leave!', node.parentNode.dataset.itemId, node.dataset.slotOrder);
         currentSlotID = '';
      })

      node.addEventListener("mouseup", (event) => {
         console.log(`CURRENT ID :: UP`, currentItemID);
         isDragging = false;
      });
   }

   function checkBoundings (mouseY, context) {
      const items = document.querySelectorAll('.drag_me_li');

      for (let item of items) {
         const itemRect = item.getBoundingClientRect();
         const placeholderRect = placeholderItem.getBoundingClientRect();
         
         const placeholderHeight = parseInt(getComputedStyle(placeholderItem).height.match(/\d+/g)[0]);
         const placeholderBottom = (placeholderRect.bottom - (placeholderHeight / 2));
         const placeholderTop = (placeholderRect.top + (placeholderHeight / 2));

         const itemHeight = parseInt(getComputedStyle(item).height.match(/\d+/g)[0]);
         const itemBottom = (itemRect.bottom - (itemHeight / 2));
         const itemTop = (itemRect.top + (itemHeight / 2));
         
         // const inside = placeholderBottom > itemTop && placeholderTop < itemBottom;
         // const above = placeholderBottom < itemTop && placeholderTop < itemBottom;
         // const beneath = placeholderBottom > itemTop && placeholderTop > itemBottom;

         const inside = mouseY > itemRect.top && mouseY < itemRect.bottom;
         const above = mouseY < itemTop;
         const beneath = mouseY > itemBottom;
         
         if (inside) {
            if (targetItemID != item.dataset.itemId) {
               targetItemID = item.dataset.itemId;
               console.log('TARGET ID', targetItemID, currentItemID);
            }

            if (above) {
               // item.style.backgroundColor = 'green';
               aboveTarget = true;
               // console.log('ABOVE', aboveTarget);
            } else {
               // item.style.backgroundColor = 'red';
               aboveTarget = false;
               // console.log('ABOVE', aboveTarget);
            }            
         } else {
            item.style.backgroundColor = 'blue';
         }
      }
   }

   onMount(() => {
      // observer = new IntersectionObserver((entries, observer) => {
      //    // Loop through all entries
      //    entries.forEach((entry) => {
      //       // If the image is in view
      //       if (entry.isIntersecting) {
      //          console.log(entry.target);
      //          // const img = entry.target;
      //          // img.src = img.dataset.src;
      //          // observer.unobserve(img);
      //       }
      //    });
      // });
   });
</script>

<svelte:document on:mousemove={handleMouseMovement} on:mouseup={handleMouseUp} />

<div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
   <!-- svelte-ignore a11y-no-static-element-interactions -->
   {#each LIST as item}
      <!-- {#if currentSlotID === `top_${item.id}`} -->
      {#if targetItemID === item.id && aboveTarget === true && isDragging}
         <!-- <div class="p-3 border-2 border-green-600 border-dashed rounded-md"></div>    -->
         {@html placeholderClone}
      {/if}

      <div
         use:observeEntry
         use:dragItem
         data-item-id={item.id}
         data-context={item.context}
         class="drag_me_li relative select-none cursor-grab focus:cursor-grabbing p-6 border-2 border-neutral-700 rounded-md"
      >
         {item.data} :: {item.id}
      </div>

      <!-- {#if currentSlotID === `bottom_${item.id}`} -->
      {#if targetItemID === item.id && aboveTarget === false && isDragging}
         {@html placeholderClone}
         <!-- <div class="p-3 border-2 border-red-600 border-dashed rounded-md"></div> -->
      {/if}
   {/each}

   {#if isDragging}
      <div bind:this={placeholderItem} class="absolute pointer-events-none p-6 border-2 left-4 right-4 border-neutral-600 border-dashed rounded-md"></div>
   {/if}
</div>



<!-- <div class="relative flex flex-col gap-4 m-6 p-4 border-2 border-neutral-700 rounded-md">
   {#each LIST as item}
      {#if currentSlotID === `top_${item.id}`}
         <div transition:slide class="p-3 border-2 border-green-600 border-dashed rounded-md"></div>   
      {/if}

      <div
         use:dragItem
         data-item-id={item.id}
         class="relative select-none cursor-grab focus:cursor-grabbing p-6 border-2 border-neutral-700 rounded-md"
      >
         {#if isDragging && item.id !== currentItemID}   
            <span
               use:observeEntry
               data-slot-order="top"
               class="absolute bg-blue-500 top-0 bottom-[50%] left-0 right-0"
            ></span>
         {/if}

         {item.data}

         {#if isDragging && item.id !== currentItemID}
            <span
               use:observeEntry
               data-slot-order="bottom"
               class="absolute bg-red-500 bottom-0 top-[50%] left-0 right-0"
            ></span>
         {/if}
      </div>

      {#if currentSlotID === `bottom_${item.id}`}
         <div transition:slide class="p-3 border-2 border-red-600 border-dashed rounded-md"></div>
      {/if}
   {/each}

   {#if isDragging}
      <div bind:this={placeholderItem} class="absolute pointer-events-none p-6 border-2 left-4 right-4 border-neutral-600 border-dashed rounded-md"></div>
   {/if}
</div> -->