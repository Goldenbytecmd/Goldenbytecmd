<!DOCTYPE html>
<html>
<head>
  <title>Mandelbrot Viewer</title>
  <style>
    canvas {
      border: 2px solid black;
      display: block;
      margin: 0 auto;
    }
    body {
      background-color: #f5f5f5;
      text-align: center;
      font-family: Arial, sans-serif;
    }
  </style>
</head>
<body>
  <h2>Mandelbrot Fractal Viewer</h2>
  <canvas id="mandelbrot" width="600" height="400"></canvas>
  <p>Scroll met je muiswiel om in/uit te zoomen – klik en sleep om te bewegen</p>

  <script>
    const canvas = document.getElementById('mandelbrot');
    const ctx = canvas.getContext('2d');
    let width = canvas.width;
    let height = canvas.height;

    let scale = 200;
    let offsetX = -width / 2;
    let offsetY = -height / 2;
    let maxIter = 100;

    function draw() {
      let imgData = ctx.createImageData(width, height);
      for (let x = 0; x < width; x++) {
        for (let y = 0; y < height; y++) {
          let a = (x + offsetX) / scale;
          let b = (y + offsetY) / scale;

          let ca = a;
          let cb = b;

          let n = 0;
          while (n < maxIter) {
            let aa = a * a - b * b;
            let bb = 2 * a * b;
            a = aa + ca;
            b = bb + cb;

            if (a * a + b * b > 16) break;
            n++;
          }

          let bright = n === maxIter ? 0 : 255 - n * 4;
          let index = (x + y * width) * 4;
          imgData.data[index + 0] = bright;
          imgData.data[index + 1] = bright;
          imgData.data[index + 2] = bright;
          imgData.data[index + 3] = 255;
        }
      }
      ctx.putImageData(imgData, 0, 0);
    }

    draw();

    // Interactief zoomen en pannen
    canvas.addEventListener("wheel", function (e) {
      e.preventDefault();
      const zoom = e.deltaY < 0 ? 1.1 : 0.9;
      scale *= zoom;
      offsetX = (offsetX - canvas.width / 2) * zoom + canvas.width / 2;
      offsetY = (offsetY - canvas.height / 2) * zoom + canvas.height / 2;
      draw();
    });

    let isDragging = false;
    let startX, startY;

    canvas.addEventListener("mousedown", function (e) {
      isDragging = true;
      startX = e.offsetX;
      startY = e.offsetY;
    });

    canvas.addEventListener("mouseup", function () {
      isDragging = false;
    });

    canvas.addEventListener("mousemove", function (e) {
      if (isDragging) {
        offsetX += e.offsetX - startX;
        offsetY += e.offsetY - startY;
        startX = e.offsetX;
        startY = e.offsetY;
        draw();
      }
    });
  </script>
</body>
</html>
