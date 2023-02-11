<script>
import { onMount } from "svelte";
let selection = ``;
let openSelectText = false;
let setObjectText = "";
let warnSelection = false;
let collectTextHTML = "Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum";
let ref;
let collectText = JSON.parse(localStorage.getItem('collectHTML')) || [];
let inputValue = "";

onMount(() => {
  document.addEventListener('selectionchange',() => {
    selection = window.getSelection(); 
  })
  document.addEventListener('DOMContentLoaded',() => {
    let spanAll = document.querySelectorAll('span')
    if (spanAll.length > 0) {
      spanAll.forEach(v => {
        v.addEventListener('click', (e) => {
          if(e.target.outerHTML.includes(e.target.dataset.textz)){
            setObjectText = e.target.dataset.textz;
          }
        })
        v.addEventListener('dblclick', (e) => {
          if(e.target.outerHTML.includes(e.target.dataset.textz)){
            let fromLocalStorage = localStorage.getItem('collectTextHTML')
            let repText = fromLocalStorage.replace(e.target.outerHTML,e.target.innerText);
            let tempCollectText = [...collectText];
            localStorage.setItem('collectTextHTML',repText)
            tempCollectText = tempCollectText.filter(item => item.selection != e.target.dataset.textz)
            collectText = tempCollectText
            console.log(tempCollectText);
            localStorage.setItem('collectHTML',JSON.stringify(collectText));
            collectTextHTML = repText
            setObjectText = "";
          }
        })
      })
    }
  })
  if (localStorage.getItem('collectTextHTML') != null) {
    collectTextHTML = localStorage.getItem('collectTextHTML');
  }
})

function selectionSlice(color){
    let text = "" 
    if(selection?.anchorOffset != 0 && selection?.focusOffset != 0){
      for (var i = 0; i < selection.rangeCount; i++) {
          text = `${color} ${selection}`;
          let selectionText = `<span style='cursor:pointer;user-select:none;background-color:${color};' data-textz='${text}'>${selection}</span>`;
          let $sNode = selection.getRangeAt(i).startContainer.parentNode;
          //var $eNode = selection.getRangeAt(i).endContainer.parentNode;
          $sNode.innerHTML = $sNode.innerHTML.replace(selection,selectionText);
          localStorage.setItem('collectTextHTML',$sNode.innerHTML);

          let checkCollectText = collectText.map(item => item.selection).includes(text);
          if(checkCollectText){
            return;
          }
          collectText = [...collectText,{ selection: text, input: "" }];
          localStorage.setItem('collectHTML',JSON.stringify(collectText));
          $sNode.onclick = function(e){
            e.preventDefault();
            if(e.target.outerHTML.includes(e.target.dataset.textz)){
              setObjectText = e.target.dataset.textz;
            }
          }
          $sNode.ondblclick = function(e){
            e.preventDefault();
            if(e.target.outerHTML.includes(e.target.dataset.textz)){
              $sNode.innerHTML = $sNode.innerHTML.replace(e.target.outerHTML,e.target.innerText);
              localStorage.setItem('collectTextHTML',$sNode.innerHTML);
              let tempCollectText = [...collectText];
              tempCollectText = tempCollectText.filter(item => item.selection != e.target.dataset.textz)
              console.log(tempCollectText);
              collectText = tempCollectText
              localStorage.setItem('collectHTML',JSON.stringify(collectText));
              setObjectText = "";
            }
          }
      }
    }
}

function handleOnSubmit(e){
  e.preventDefault();
  let findCollectText = collectText.find(item => item.selection === setObjectText)
  if (findCollectText) {
    let items = collectText.map(item => {
      if (item.selection === setObjectText) {
        return {
          ...item,
          input: inputValue
        }
      }
      return item;
    })
    collectText = [...items]
    localStorage.setItem('collectHTML',JSON.stringify(collectText));
  }
  inputValue = ""
}

$: readInputValue = collectText.find((item) => item.selection === setObjectText)

</script>

<div class="box">
  <h3>Example Selected Text and Take Note</h3>
  <div>{@html collectTextHTML}</div>
  {#if selection != ""}
    <div class="buttons">
      <button type="button" on:click={() => selectionSlice('blue')}>blue</button>
      <button type="button" on:click={() => selectionSlice('red')}>red</button>
    </div>
  {/if}
  {#if setObjectText != "" && selection == ""}
    <div class="openSelectText">
      <div>
          #{setObjectText},
          {#if readInputValue.input != ""}
            Note: {readInputValue.input}
          {/if}
      </div>
      <form on:submit={handleOnSubmit}>
        <input bind:value={inputValue} placeholder="Note" />
        <button type="submit">Save</button>
      </form>
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
  flex-direction: column;
  margin-top:5px;
}

@media only screen and (max-width: 576px) {
  .box {
    width: 100%;
  }
}

</style>
