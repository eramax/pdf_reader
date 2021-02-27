<script>
 import Pdf from "./Pdf1.svelte";
 let url =
   "https://uploads.codesandbox.io/uploads/user/788432d2-e404-445a-9edb-09d901099290/8qUO-pdd.pdf";
</script>

<style>
  main {
    font-family: sans-serif;
    text-align: center;
  }
</style>

<main>
	<h1>Hello CodeSandbox</h1>
	<Pdf {url}/>
</main>