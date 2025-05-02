<script>
  /** @type {{
   *   afterDownloadPage: boolean,
   *   inputUrl: string
   * }} */
  let { afterDownloadPage = $bindable(), inputUrl = $bindable() } = $props();
</script>

<div class="max-w-4xl text-center">
  <h1 class="text-3xl font-bold">
    Your download has <span
      class="bg-linear-to-r from-red-700 via-yellow-600 to-pink-600 bg-clip-text text-transparent"
      >Started</span
    >
  </h1>
  <div class="space-y-2 py-4">
    <p class="text-2xl">
      If you found this site useful, please consider <a
        href="https://github.com/TheOnlyWayUp/WattpadDownloader"
        target="_blank"
        class="link"
        data-umami-event="Star">starring the project</a
      > to support WattpadDownloader.
    </p>
    <p class="pt-2 text-lg">
      You can also join us on <a
        href="https://discord.gg/P9RHC4KCwd"
        target="_blank"
        class="link"
        data-umami-event="Discord">discord</a
      >, where we release features early and discuss updates.
    </p>
  </div>
  <div class="grid grid-rows-2 justify-center gap-y-10">
    <a
      href="/donate"
      target="_blank"
      class="btn mt-10 bg-cyan-200 btn-lg hover:bg-green-200"
      >Buy me a Coffee! 🍵</a
    >
    <button
      onclick={() => {
        afterDownloadPage = false;
        inputUrl = "";
      }}
      class="btn btn-outline btn-lg"
      >Download More
    </button>
  </div>
</div>
