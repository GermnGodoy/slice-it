<script>
  import WidthIcon from "../components/WidthIcon.svelte";
  import RangeSlider from "svelte-range-slider-pips";
  import Dropzone from "svelte-file-dropzone";
  import { panzoom } from 'svelte-pan-zoom';
  import { writable } from "svelte/store";

  let files = {
    accepted: [],
    rejected: []
  };

  let path = './pato.jpg'
  let promise = null

  function handleFilesSelect(e) {
    const { acceptedFiles, fileRejections } = e.detail;
    files.accepted = [...files.accepted, ...acceptedFiles];
    files.rejected = [...files.rejected, ...fileRejections];
    const reader = new FileReader();

    path = URL.createObjectURL(files.accepted[0])
    console.log(path)
    promise = new Promise(resolve => {
      const image = new Image()
  
      image.onload = () =>
        resolve({
          width: image.width*2,
          height: image.height*2,
          render,
        })
      image.src = path
  
      function render(ctx, _t, _focus) {
        ctx.drawImage(image, 0, 0)
      }
      return
    })
    return promise
  }
  
  let width = 50
  let height = 50
  const title = "Hellow"

  const handleWidthChange = (e) => {
    width = e.detail.value
  }

  const handleHeightChange = (e) => {
    height = e.detail.value
  }



</script>

	<head>
		<meta charset="UTF-8" />
		<meta name="description" content="Astro description" />
		<meta name="viewport" content="width=device-width" />
		<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
		<title>{title}</title>
	</head>
	<div class="container">
    <aside class="side-menu">
      <h3>Options</h3>
      <div class="size-options">
        <div class="option">
          <WidthIcon size={30} color='#121212' rotation={0}/>
          <p>{width}</p>
          <RangeSlider on:change={handleWidthChange} values={[50]} min={0} max={100}/>
        </div>
        <div class="option">
          <WidthIcon size={30} color='#121212' rotation={90}/>
          <p>{height}</p>
          <RangeSlider on:change={handleHeightChange} values={[50]} min={0} max={100}/>
        </div>
      </div>
    </aside>
    <main>
      {#if files.accepted.length === 0}
        <Dropzone on:drop={handleFilesSelect} containerClasses="droplace" disableDefaultStyles={true} multiple={false}>
          Images you want to slice :)
        </Dropzone>
      {/if}
    </main>
    {#if files.accepted.length !== 0}
      {#await promise then options}
        <canvas use:panzoom={options} />
      {/await}
    {/if}
	</div>

<style>

:root {
    --range-slider:            hsl(0, 0%, 100%);
    --range-handle-inactive:   hsl(194.1, 97.7%, 65.9%);
    --range-handle:            hsl(194.1, 97.7%, 65.9%);
    --range-handle-focus:      hsl(194.1, 97.7%, 65.9%);
    --range-handle-border:     hsl(234, 67.6%, 71%);
    --range-range-inactive:    hsl(194.1, 97.7%, 65.9%);
    --range-range:             hsl(194.1, 97.7%, 65.9%);
    --range-float-inactive:    hsl(194.1, 97.7%, 65.9%);
    --range-float:             hsl(194.1, 97.7%, 65.9%);
    --range-float-text:        hsl(0, 0%, 100%);

    --range-pip:               hsl(210, 14.3%, 53.3%);
    --range-pip-text:          hsl(210, 14.3%, 53.3%);
    --range-pip-active:        hsl(180, 25.4%, 24.7%);
    --range-pip-active-text:   hsl(180, 25.4%, 24.7%);
    --range-pip-hover:         hsl(180, 25.4%, 24.7%);
    --range-pip-hover-text:    hsl(180, 25.4%, 24.7%);
    --range-pip-in-range:      hsl(180, 25.4%, 24.7%);
    --range-pip-in-range-text: hsl(180, 25.4%, 24.7%);
  }

  :global(.rangeSlider) {
    font-size: 8px;
    width: 100%;
  }

  canvas {
		box-sizing: border-box;
    position: absolute;
		width: 100%;
		height: 100%;
		margin: 0;
		padding: 0;
		user-select: none;
		touch-action: none;
		background-color: transparent;
		overscroll-behavior: none;
		-webkit-user-select: none; /* disable selection/Copy of UIWebView */
		-webkit-touch-callout: none; /* disable the IOS popup when long-press on a link */
    z-index: 1;
	}

  html {
    width: 100%;
    height: 100%;
  }
  
  body {
    width: 100%;
    height: 100%;
  }
  
  :global(.droplace) {
    margin-right: 20px;
    margin-left: 10px;
    margin-top: 2px;
    text-align: center;
    width: 100%;
    height: 94%;
    background-color: rgba(0, 0, 0, 0.05);
    color: rgba(0, 0, 0, 0.4);
    border: rgba(0, 0, 0, 0.4);
    border-width: 2px;
    border-radius: 10px;
    border-style: dashed;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: medium;
    font-weight: 500;
  }

	.container {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    display: grid;
    width: 100%;
    height: 100%;
    margin: 0;
    grid-template-columns: minmax(200px, 2fr) 5fr;
    grid-template-areas: "side-menu drop";
    background: rgb(191,255,246);
    background: radial-gradient(circle, rgba(191,255,246,0.5) 0%, rgba(235,255,252,0.2) 43%, rgba(255,255,255,1) 100%);
    margin: 0;
  }

  .side-menu {
    grid-area: side-menu;
    background-color: #f5f5f5;
    transition: all 1s;
    border-radius: 15px;
    margin: 10px;
    box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    display: flex;
    align-items: center;
    flex-direction: column;
    padding-inline: 10px;
    z-index: 10;
  }

  .side-menu > h3 {
    color: #1f1f1f;
    font-size: x-large;
    margin-top: 12px;
    margin-bottom: 10px;
  }

  .side-menu > div {
    padding: auto;
    margin-inline: auto;
    box-shadow: rgba(0, 0, 0, 0.1) 2px 2px 2px;
    width: 100%;
    min-height: 35px;
    border-radius: 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
  }

  .side-menu>div {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    width:100%;
  }

  main {
    grid-area: drop;
    transition: all 1s;
    width: 100%;
    height: 100%;
    display: flex;
    justify-items: center;
    align-items: center;
    justify-items: center;
  }

  .option {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    margin: 0px;
  }

  .option > p {
    width: 30%;
    text-align: center;
    font-size: small;
    margin: 0;
  }

  .size-options {
    display: flex;
    flex-wrap: wrap;
    gap: -10px;
  }

</style>
