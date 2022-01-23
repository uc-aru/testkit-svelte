<script>
    export let Heading;
</script>

<link rel="stylesheet" href="style.css">
<div class="nav-bar pos-fixed">
  <div class="img">
    <img src="/images/ucertifyLogo.png" alt="ucertify"  height="70px" width="110vw" class='image' />
  </div>
  <div class="main-heading pos-fixed" tabindex="0" aria-label={Heading}>
    <h1>{Heading}</h1>
  </div>
</div>
<style>
  .image{
    margin-top: 3px;
  }
</style>