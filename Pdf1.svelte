<script>
  import * as pdfjsLib from "pdfjs-dist";
  import pdfWorker from "pdfjs-dist/build/pdf.worker.entry";
  import "pdfjs-dist/web/pdf_viewer.css";
  import * as pdfjsViewer from "pdfjs-dist/web/pdf_viewer";
  import { onMount } from "svelte";
  import Header from "./Header.svelte";

  export let url;

  let cMapUrl = `//cdn.jsdelivr.net/npm/pdfjs-dist@${pdfjsLib.version}/cmaps/`;
  let cMapPacked = true;
  let SEARCH_FOR = "book";

  const GetPage = async (pdfDoc, num, scale) => {
    pageRendering = true;
    let pg = await pdfDoc.getPage(num);
    return pg;
  };

  async function loadPdf(link) {
    console.log(link);
    return await pdfjsLib.getDocument({
      url: link,
      cMapUrl: cMapUrl,
      cMapPacked: cMapPacked
    }).promise;
  }

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
      annotationLayerFactory: new pdfjsViewer.DefaultAnnotationLayerFactory(),
      linkService: pdfLinkService,
      findController: pdfFindController,
      removePageBorders: true,
      enableWebGL: true,
      renderer: "svg",
      enhanceTextSelection: true,
      textLayerMode: 2
    });

    pdfLinkService.setViewer(pdfViewer);

    eventBus.on("pagesinit", function() {
      // We can use pdfViewer now, e.g. let's change default scale.
      pdfViewer.currentScaleValue = "page-width"; //page-fit
      // We can try searching for things.
      if (SEARCH_FOR) {
        pdfFindController.executeCommand("find", { query: SEARCH_FOR });
      }
    });

    loadPdf(url).then(pdf => {
      pdfViewer.setDocument(pdf);
      pdfLinkService.setDocument(pdf, null);
    });
  });
</script>
<style>
  #viewerContainer {
    overflow: auto;
    position: absolute;
    top: 10%;
    width: 83%;
    height: 80%;
  }
</style>

<div class="flex flex-col w-full overflow-hidden ">
<Header />
<div class="w-full" id="viewerContainer">
  <div id="viewer" class="pdfViewer" />
</div>
</div>
