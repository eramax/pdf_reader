<script>
  import * as pdfjsLib from "pdfjs-dist";
  import pdfWorker from "pdfjs-dist/build/pdf.worker.entry";
  import "pdfjs-dist/web/pdf_viewer.css";
  import * as pdfjsViewer from "pdfjs-dist/web/pdf_viewer";
  import { onMount } from "svelte";

  export let url;

  let cMapUrl = `//cdn.jsdelivr.net/npm/pdfjs-dist@${pdfjsLib.version}/cmaps/`;
  let cMapPacked = true;

  onMount(async () => {
    //pdfjsLib.GlobalWorkerOptions.workerSrc = pdfWorker;
    var eventBus = new pdfjsViewer.EventBus();
    var container = document.getElementById("viewerContainer");
    // (Optionally) enable hyperlinks within PDF files.
    var pdfLinkService = new pdfjsViewer.PDFLinkService({
      eventBus
    });

    // (Optionally) enable find controller.
    var pdfFindController = new pdfjsViewer.PDFFindController({
      eventBus,
      linkService: pdfLinkService
    });

    var pdfViewer = new pdfjsViewer.PDFViewer({
      container,
      eventBus,
      linkService: pdfLinkService,
      findController: pdfFindController
    });

    pdfLinkService.setViewer(pdfViewer);

    eventBus.on("pagesinit", function() {
      // We can use pdfViewer now, e.g. let's change default scale.
      pdfViewer.currentScaleValue = "page-width";

      // We can try searching for things.
      // if (SEARCH_FOR) {
      //   pdfFindController.executeCommand("find", { query: SEARCH_FOR });
      // }
    });

    // Loading document.
    var loadingTask = pdfjsLib.getDocument({
      url: url,
      cMapUrl: cMapUrl,
      cMapPacked: cMapPacked
    });
    loadingTask.promise.then(function(pdfDocument) {
      // Document loaded, specifying document for the viewer and
      // the (optional) linkService.
      pdfViewer.setDocument(pdfDocument);

      pdfLinkService.setDocument(pdfDocument, null);
    });
  });
</script>
<style>
  #viewerContainer {
    overflow: auto;
    position: absolute;
    width: 100%;
    height: 100%;
  }
</style>
<div id="viewerContainer">
  <div id="viewer" class="pdfViewer" />
</div>
