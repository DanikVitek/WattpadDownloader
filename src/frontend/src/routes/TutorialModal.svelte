<script>
  /** @type {{ ref: HTMLDialogElement }} */
  let { ref = $bindable() } = $props();
</script>

<dialog class="modal motion-reduce:transition-none" bind:this={ref}>
  <div class="modal-box">
    <form method="dialog">
      <button class="btn absolute top-2 right-2 btn-circle btn-ghost btn-sm"
        >✕</button
      >
    </form>
    <h3 class="text-lg font-bold">Finding the Story URL</h3>
    <ol class="list list-inside list-disc space-y-4 py-4">
      <li>
        Copy the URL from the Website, or hit share and copy the URL on the App.
      </li>
      <li>
        For example,
        <span class="bg-slate-100 p-1 font-mono"
          >wattpad.com/<span class="rounded-xs bg-amber-200">story</span
          >/237369078-wattpad-books-presents</span
        >.
      </li>
      <li>
        <span class="bg-slate-100 p-1 font-mono"
          >https://www.wattpad.com/939103774-given</span
        > is okay too.
      </li>
      <li>Paste the URL and hit Download!</li>
    </ol>
  </div>
  <form method="dialog" class="modal-backdrop">
    <button>close</button>
  </form>
</dialog>
