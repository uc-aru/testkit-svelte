<script>
  import Nav from "./UI/Navigation.svelte";
  import Button from "./UI/Button.svelte";
  import Footer from "./UI/Footer.svelte";
  import EndTestModal from "./UI/Modal.svelte";
  import Result from "./UI/Result.svelte";
  import List from "./UI/Listing.svelte";
  import { current } from "./dataStore.js";
  import { currentitem } from "./dataStore.js";
  import { counter } from "./dataStore.js";
  import { startpage,isconfirm } from "./dataStore.js";
  import { list } from "./dataStore.js";
  import { isopen } from "./dataStore.js";
  var startloading = false;
  var timer;
  var isend = false;
  var firstpage = false;

  function tooglestartpage() {
    startloading = true;
    timer = setInterval(() => {
      startpage.set(false);
    }, 1000);
    firstpage = true;
    counter.set(0);
    current.set(0);
    currentitem.set(0);
  }

  function open() {
    $isopen = true;
  }

  function cancelit() {
    $isopen = !$isopen;
  }

  function closelist() {
    list.set(false);
    firstpage = true;
  }

  function confirm() {
    isconfirm.set(true);
    $isopen = false;
    startpage.set(true);
    firstpage = true;
    currentitem.set(0);
    startloading = false;
    clearInterval(timer);
    localStorage.clear();
  }

  function listopen() {
    list.set(true);
  }

  function next() {
    $current += 1;
    $counter += 1;
    $currentitem += 1;
  }

  function prev() {
    $counter -= 1;
    $current -= 1;
    $currentitem -= 1;
  }

  function end() {
    isend = true;
  }

  function review() {
    isconfirm.set(false);
  }

</script>

<link rel="stylesheet" href="style.css" />
<header>
  <Nav Heading={"uCertify Test Prep"} />
</header>
<main>
  {#if $startpage}
    {#if startloading}
      <div class="icon-backdrop" />
      <i class="fa fa-spinner fa-spin icon" />
    {/if}

    {#if $isconfirm}
      <Button class="success" type="button" id="Start" name="Start" caption="Start Test" on:click={tooglestartpage} />
      <Result on:res={review} />
    {:else}
    <!-- s for Start the test the button -->
      <Button style="button" margin="btn" type="button" id="Start" name="Start" accesskey=s caption="Start Test" on:click={tooglestartpage} />
    {/if}

  {:else if !$startpage}
    {#if firstpage}
      <Footer on:l={listopen} on:click={open} on:n={next} on:p={prev} on:e={end} />
    {/if}
  {/if}

  {#if $isopen}
    <EndTestModal on:cancel={cancelit} on:confirm={confirm} />
  {/if}

  {#if $list}
    <List on:cancel={closelist} />
  {/if}
</main>