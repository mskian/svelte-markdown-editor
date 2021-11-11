<script>
  import { createEventDispatcher, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();
  import { toLocalStorage } from './utils.js';

  // transition to fade in the icon to show the file has been saved
  import { fly } from 'svelte/transition';

  // initialize a variable from the markdown file
  export let markdown;
  let value = markdown;

  // variable bound to the text area, in order to scroll at the bottom when appending elements
  let textarea;

  // function adding the input string in the textarea element, and at the point specified by the cursor
  function updateValue(string) {
    const { selectionEnd } = textarea;
    value = `${value.slice(0, selectionEnd)}${string}${value.slice(
      selectionEnd
    )}`;
  }

  // following a click on the code button, append a series of backtick
  function handleCode() {
    const block = `\n\`\`\`\n\n\`\`\`\n`;
    updateValue(block);
  }

  // following a click on the link button, append the syntax for url []()
  function handleLink() {
    const link = '[link](url)';
    updateValue(link);
  }

   function handleimage() {
    const image = '![](http://www.example.com/image.jpg)';
    updateValue(image);
  }

   function handlehead() {
    const heading = '#';
    updateValue(heading);
  }

  // following a click on the save button toggle a boolean to change the icon and call the function to save to local storage
  let isSaved = false;
  function handleSave() {
    isSaved = true;
    toLocalStorage(value);
  }

  // following the input event on the textarea remove the saved icon to show the default action
  function handleInput(e) {
    if (isSaved) {
      isSaved = false;
    }
  }

  // following a click on the preview button, dispatch the event to show the preview
  function handlePreview() {
    // pass the value to the component to update the markdown
    dispatch('preview', value);
  }
</script>
<form action="http://localhost:3005/data" method="post" role="form" enctype="application/x-www-form-urlencoded">
<div class="field">
<div class="control">
<label class="label has-text-weight-bold" for="Post title">Post title</label>
<input type="text" id="title" name="title" class="input is-link" placeholder="Post title">
</div>
</div>
<div class="field">
<div class="control">
<label class="label has-text-weight-bold" for="Post description">Post Description</label>
<textarea class="input input-description is-link" type="text" id="description" name="description" placeholder="Post title" />
</div>
</div>
<div class="field">
<div class="control">
<label class="label has-text-weight-bold" for="Post Content">Post Markdown Content</label>
<nav>
  <p><b>Edit tools - &nbsp;&nbsp; </b></p>
  <!-- svelte-ignore a11y-missing-attribute -->
  <a
    on:click={handleCode}
    title="Add code block"
    aria-label="Add code block"
  >
  🖥
  </a>
  &nbsp;&nbsp;
  <!-- svelte-ignore a11y-missing-attribute -->
  <a on:click={handleLink} title="Add link" aria-label="Add link">
    🌐
  </a>
  &nbsp;&nbsp;
  <!-- svelte-ignore a11y-missing-attribute -->
  <a on:click={handlehead} title="Add Heading" aria-label="Add Heading">
    👔
  </a>
  &nbsp;&nbsp;
  <!-- svelte-ignore a11y-missing-attribute -->
  <a on:click={handleimage} title="Add image" aria-label="Add image">
    🖼
  </a>
  &nbsp;&nbsp;
  <!-- svelte-ignore a11y-missing-attribute -->
  <a
    on:click={handleSave}
    title="Save to local storage"
    aria-label="Save to local storage"
  >
    {#if isSaved}
    ☑
    {:else}
    💾
    {/if}
    </a>
    &nbsp;&nbsp;
  <!-- on click dispatch the preview event to show the appropriate component -->
  <!-- svelte-ignore a11y-missing-attribute -->
  <a
    on:click={handlePreview}
    title="Preview markdown"
    aria-label="Download markdown"
  >
   🕵
  </a>
</nav>

<textarea class="input input-editor is-link" bind:this={textarea} bind:value on:input={handleInput} name="postcontent" id="postcontent" />
</div>
</div>

<div class="field">
<div class="control">
<label class="label has-text-weight-bold" for="Post tag">Post tag</label>
<input class="input is-link "type="text" id="tag" name="tag" placeholder="Add tag">
</div>
</div>
<div class="field">
<div class="control">
<div class="text-center">
<button type="submit" class="btn purple">Create Post</button>
</div>
</div>
</div>
</form>
<br>