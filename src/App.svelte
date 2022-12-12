<script lang="ts">
  import { empty, fromEvent, timer } from 'rxjs';
  import { debounce } from 'rxjs/operators';
  import 'xp.css/dist/XP.css';
  import {
    ENABLE_RANDOM_ERROR,
    ERROR_CHANCE_PERCENT,
    SHOW_LOADING_SCREEN,
  } from './appConfig';
  import LoadingScreen from './LoadingScreen.svelte';
  import isMobile from './util/isMobile';

  let wWidth = '40vw';
  let rWidth = '50vw';
  let aWidth = '60vw';
  if (isMobile()) {
    wWidth = '5vw';
    rWidth = '35vw';
    aWidth = '65vw';
  }
  function handleDragDrop(e) {
    e.preventDefault();
  }
  function shouldShowBlueScreenError() {
    return Math.random() * 100 <= ERROR_CHANCE_PERCENT;
  }

  let loading = SHOW_LOADING_SCREEN;
  setTimeout(() => (loading = false), 1500);

  let showScreenSaver = false;
  // Screen saver doesn't work on mobile
  if (!isMobile()) {
    const mouseMoveStream = fromEvent(document, 'mousemove').pipe(
      debounce((e) => (showScreenSaver ? empty() : timer(30000)))
    );
    mouseMoveStream.subscribe(() => {
      showScreenSaver = !showScreenSaver;
    });
    // Start the stream
    document.dispatchEvent(new Event('mousemove'));
  }

  let showBlueScreenError = false;
  const intervalId = setInterval(() => {
    let showError = shouldShowBlueScreenError();
    if (showError && !loading && !showScreenSaver && ENABLE_RANDOM_ERROR) {
      showBlueScreenError = true;
      clearInterval(intervalId);
    }
  }, 5000);
</script>

{#if loading}
  <LoadingScreen />
{/if}
<div class="container">
  <div class="screen" on:drop={handleDragDrop} ondragover="return false">
    Hello Heather! This is proof that I can build things sometimes. Sometimes they work.
  </div>
</div>

<style>
  .screen {
    position: relative;
    background: url('../images/bliss.png') center/cover no-repeat #0c8dea;
  }

  .container {
    height: 100%;
    text-align: center;
  }
  .container .screen {
    width: 100%;
    height: 100%;
    overflow: hidden;
    display: inline-block;
    vertical-align: middle;
    text-align: left;
  }

  * {
    box-sizing: border-box;
    user-select: none;
    -webkit-user-select: none;
    cursor: default;
  }

  html,
  body {
    margin: 0;
    width: 100%;
    height: 100%;
    font-family: verdana;
  }
</style>
