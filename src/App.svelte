<script>
import { onMount } from "svelte";
let selection = ``;
let openSelectText = false;
let setObjectText = "";
let warnSelection = false;

onMount(() => {
  document.addEventListener('selectionchange',() => {
    selection = document.getSelection();
  })
})

function selectionSlice(color){
  if(selection === "") {
    warnSelection = true
  }else{
    let text = "" 
    warnSelection = false;
    for (var i = 0; i < selection.rangeCount; i++) {
        text = `${color} ${selection}`;
        var $sNode = selection.getRangeAt(i).startContainer.parentNode;
        var $eNode = selection.getRangeAt(i).endContainer.parentNode;
        $sNode.innerHTML = $sNode.innerHTML.replace(selection,`<span style='cursor:pointer;background-color:${color};' data-textZ='${text}'>${selection}</span>`);
        $sNode.onclick = function(e){
          openSelectText = !openSelectText;
          setObjectText = e.target.dataset.textz;
        }
    }
  }
}

</script>

<div class="box">
  <h3>Example Selected Text and Anotation</h3>
  {#if warnSelection}
    <p>Please Select the text and click the button, don't just click</p>
  {/if}
  <div class="buttons">
    <button type="button" on:click={() => selectionSlice('blue')}>blue</button>
    <button type="button" on:click={() => selectionSlice('red')}>red</button>
  </div>
  <div>
Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum
  </div>
  {#if openSelectText}
    <div class="openSelectText">
      <div>#{setObjectText}</div>
    </div>
  {/if}
</div>

<style>
.buttons {
  display:flex;
  flex-direction:row;
  margin-bottom: 5px;
}
.buttons button {
    margin-right: 5px;
}
.box {
  width: 50%;
  margin-left:auto;
  margin-right:auto;
  display: flex;
  flex-direction:column;
  justify-content: center;
  align-items:center;
  min-height: 100vh;
}

.openSelectText {
  display:flex;
  flex-direction: row;
}
</style>
