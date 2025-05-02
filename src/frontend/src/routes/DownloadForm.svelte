<script>
  /** @type {{
   *   afterDownloadPage: boolean,
   *   inputUrl: string,
   *   storyURLTutorialModal: HTMLDialogElement,
   * }} */
  let {
    afterDownloadPage = $bindable(),
    inputUrl = $bindable(""),
    storyURLTutorialModal,
  } = $props();

  let downloadImages = $state(false);
  let downloadAsPdf = $state(false); // 0 = epub, 1 = pdf
  let isPaidStory = $state(false);
  let invalidUrl = $state(false);
  let credentials = $state({
    username: "",
    password: "",
  });
  let downloadId = $state("");
  /** @type {"story" | "part" | ""} */
  let mode = $state("");

  let buttonDisabled = $derived(
    !inputUrl ||
      (isPaidStory && !(credentials.username && credentials.password)),
  );

  let url = $derived(
    `/download/` +
      downloadId +
      `?om=1` +
      (downloadImages ? "&download_images=true" : "") +
      (isPaidStory
        ? `&username=${encodeURIComponent(credentials.username)}&password=${encodeURIComponent(credentials.password)}`
        : "") +
      `&mode=${mode}` +
      (downloadAsPdf ? "&format=pdf" : "&format=epub"),
  );

  /**
   * @param {string} input
   * @param {HTMLInputElement} [inputElement]
   */
  const setInputAsValid = (input, inputElement) => {
    invalidUrl = false;
    inputUrl = input;
    downloadId = input;
    if (inputElement) inputElement.value = input;
  };

  /**
   * @param {string} input
   * @param {HTMLInputElement} inputElement
   */
  const setInputAsInvalid = (input, inputElement) => {
    invalidUrl = true;
    inputUrl = input;
    downloadId = input;
    inputElement.value = input;
  };

  /** @type {import("svelte/elements").FormEventHandler<HTMLInputElement>} */
  const onInputUrl = (e) => {
    let input = e.currentTarget.value.toLowerCase();

    if (!input) {
      setInputAsValid("");
      return;
    }

    if (/^\d+$/.test(input)) {
      // All numbers
      mode = "story";
      setInputAsValid(input, e.currentTarget);
      return;
    }

    if (!input.includes("wattpad.com/")) {
      setInputAsInvalid(input.match(/\d+/g)?.join("") ?? "", e.currentTarget);
      return;
    }

    // Is a string and contains wattpad.com/

    if (input.includes("/story/")) {
      // https://wattpad.com/story/237369078-wattpad-books-presents
      mode = "story";
      setInputAsValid(
        input.split("-", 1)[0].split("?", 1)[0].split("/story/")[1], // removes tracking fields and title
        e.currentTarget,
      );
    } else if (input.includes("/stories/")) {
      // https://www.wattpad.com/api/v3/stories/237369078?fields=...
      mode = "story";
      setInputAsValid(
        input.split("?", 1)[0].split("/stories/")[1], // removes params
        e.currentTarget,
      );
    } else {
      // https://www.wattpad.com/939051741-wattpad-books-presents-the-qb-bad-boy-and-me
      input = input.split("-", 1)[0].split("?", 1)[0].split("wattpad.com/")[1]; // removes tracking fields and title
      if (/^\d+$/.test(input)) {
        // If "wattpad.com/{downloadId}" contains only numbers
        mode = "part";
        setInputAsValid(input, e.currentTarget);
      } else {
        setInputAsInvalid("", e.currentTarget);
      }
    }

    // Originally, I was going to call the Wattpad API (wattpad.com/api/v3/stories/${story_id}), but Wattpad kept blocking those requests. I suspect it has something to do with the Origin header, I wasn't able to remove it.
    // In the future, if this is considered, it would be cool if we could derive the Story ID from a pasted Part URL. Refer to @AaronBenDaniel's https://github.com/AaronBenDaniel/WattpadDownloader/blob/49b29b245188149f2d24c0b1c59e4c7f90f289a9/src/api/src/create_book.py#L156 (https://www.wattpad.com/api/v3/story_parts/{part_id}?fields=url).
  };
</script>

<div class="card w-full max-w-sm shrink-0 bg-base-100 shadow-2xl">
  <form class="card-body">
    <fieldset class="fieldset">
      <input
        type="text"
        placeholder="Story URL"
        class="input w-full"
        class:input-warning={invalidUrl}
        oninput={onInputUrl}
        required
        name="input_url"
      />
      <label class="label" for="input_url">
        {#if invalidUrl}
          <span class="text-red-500">
            Refer to (<button
              class="link font-semibold"
              onclick={() => storyURLTutorialModal.showModal()}
              data-umami-event="Part StoryURLTutorialModal Open"
              >How to get a Story URL</button
            >).
          </span>
        {:else}
          <button
            class="link text-sm font-semibold text-base-content"
            onclick={() => storyURLTutorialModal.showModal()}
            data-umami-event="StoryURLTutorialModal Open"
            >How to get a Story URL</button
          >
        {/if}
      </label>

      <label class="label cursor-pointer justify-between">
        <span class="text-sm text-base-content"
          >This is a Paid Story, and I've purchased it</span
        >
        <input
          type="checkbox"
          class="checkbox checkbox-warning shadow-md"
          bind:checked={isPaidStory}
        />
      </label>
      {#if isPaidStory}
        <label class="input flex w-full items-center gap-2">
          Username
          <input
            type="text"
            class="grow"
            name="username"
            placeholder="foxtail.chicken"
            bind:value={credentials.username}
            required
          />
        </label>
        <label class="input flex w-full items-center gap-2">
          Password
          <input
            type="password"
            class="grow"
            placeholder="supersecretpassword"
            name="password"
            bind:value={credentials.password}
            required
          />
        </label>
      {/if}
    </fieldset>

    <fieldset class="mt-6 fieldset">
      <a
        class="btn rounded-l-none"
        class:btn-primary={!downloadAsPdf}
        class:btn-secondary={downloadAsPdf}
        class:btn-disabled={buttonDisabled}
        data-umami-event="Download"
        href={url}
        onclick={() => (afterDownloadPage = true)}>Download</a
      >

      <!-- <label class="swap w-fit label mt-2">
        <input type="checkbox" bind:checked={downloadAsPdf} />
        <div class="swap-on">
          Downloading as <span class=" underline text-bold">PDF</span> (Click)
        </div>
        <div class="swap-off">
          Downloading as <span class=" underline text-bold">EPUB</span> (Click)
        </div>
      </label> -->

      <label class="label cursor-pointer justify-between">
        <span class="text-sm text-base-content"
          >Include Images (<strong>Slower Download</strong>)</span
        >
        <input
          type="checkbox"
          class="checkbox checkbox-warning shadow-md"
          bind:checked={downloadImages}
        />
      </label>
    </fieldset>
  </form>
</div>
