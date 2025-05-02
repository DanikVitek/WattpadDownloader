<script>
  import DownloadForm from "./DownloadForm.svelte";
  import InfoCard from "./InfoCard.svelte";
  import AfterDownloadPage from "./AfterDownloadPage.svelte";
  import TutorialModal from "./TutorialModal.svelte";

  let inputUrl = $state("");
  let afterDownloadPage = $state(false);

  /** @type {HTMLDialogElement} */
  let storyURLTutorialModal = $state();
</script>

<div>
  <div class="hero min-h-screen">
    <div
      class="hero-content flex-col-reverse rounded-sm bg-base-100/50 py-32 shadow-xs lg:flex-row lg:p-16"
    >
      {#if !afterDownloadPage}
        <DownloadForm
          bind:afterDownloadPage
          bind:inputUrl
          {storyURLTutorialModal}
        />
        <InfoCard />
      {:else}
        <AfterDownloadPage bind:afterDownloadPage bind:inputUrl />
      {/if}
    </div>
  </div>
</div>

<TutorialModal bind:ref={storyURLTutorialModal} />
