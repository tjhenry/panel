<script>
  import { onMount } from "svelte";
  let color = "#ff0000";
  let panelImage = "/Panel1000.png";
  let maskImage = "/Mask1000.png";

  let image1 = new Image();
  let image2 = new Image();
  var mask = new Image();

  // Get the canvas element and its context
  let ctx;
  var maskData = null;

  function hexToRgb(hex) {
    // Convert the hex code to an integer
    const int = parseInt(hex.substring(1), 16);

    // Extract the red, green, and blue values
    const r = (int >> 16) & 255;
    const g = (int >> 8) & 255;
    const b = int & 255;

    // Return the RGB values as an object
    return { r, g, b };
  }

  function updateColor() {
    console.log(color);
    // Things need to be initialized
    let newColor = hexToRgb(color);
    console.log(newColor);

    if (ctx && maskData) {
      for (var x = 0; x < 1000; x++) {
        for (var y = 0; y < 1000; y++) {
          var index = (x + y * 1000) * 4; // Get the pixel index

          // Check if the pixel in the mask is pink
          if (
            maskData.data[index] != null &&
            maskData.data[index + 1] != null &&
            maskData.data[index + 2] != null
          ) {
            if (
              maskData.data[index] === 255 &&
              maskData.data[index + 1] === 0 &&
              maskData.data[index + 2] === 242
            ) {
              // Apply the color overlay to the corresponding pixel in the first image
              ctx.fillStyle =
                "rgba(" +
                newColor.r +
                "," +
                newColor.g +
                "," +
                newColor.b +
                ", .75)";
              ctx.fillRect(x, y, 1, 1);
            }
          }
        }
      }
    }
  }

  onMount(() => {
    const canvas = document.getElementById("canvas");
    ctx = canvas.getContext("2d");

    canvas.width = 1000;
    canvas.height = 1000;

    image1.src = panelImage;
    image1.onload = function () {
      ctx.drawImage(image1, 0, 0);
    };

    mask.src = maskImage;

    mask.onload = function () {
      // Create a temporary canvas to get the ImageData from the mask
      var tempCanvas = document.createElement("canvas");
      tempCanvas.width = 1000;
      tempCanvas.height = 1000;

      var tempContext = tempCanvas.getContext("2d");
      tempContext.drawImage(mask, 0, 0);

      // Get the ImageData object from the temporary canvas
      maskData = tempContext.getImageData(0, 0, 1000, 1000);
    };

    image2.src = maskImage;
    image2.onload = function () {
      // Loop through every pixel in the mask and apply the color overlay to the first image
      console.log("image2 onload");
      //this.updateColor();
    };
  });
</script>

<div class="container">
  <div>
    <h2>N177BW</h2>
  </div>
  <div>
    <label>
      Select Paint Color:
      <input type="color" bind:value={color} on:input={() => updateColor()} />
    </label>
  </div>
  <div>
    <canvas id="canvas" width="1000" height="1000" />
  </div>
</div>

<style>
  .container {
    padding-top: 5rem;
    position: relative;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  canvas {
    display: flex;
    justify-content: center;
    align-items: center;
    max-width: 90%;
    max-height: 90%;
  }
</style>
