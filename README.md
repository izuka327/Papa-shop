<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Scratch & Win</title>
  <style>
    body {
      background: #f5f5f5;
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 30px;
    }
    .container {
      display: inline-block;
      position: relative;
    }
    #scratchCanvas {
      border: 2px solid #ccc;
      border-radius: 10px;
      cursor: crosshair;
    }
    .message {
      margin-top: 20px;
      font-size: 1.2rem;
      font-weight: bold;
      color: green;
    }
  </style>
</head>
<body>
  <h1>🎉 Scratch & Win 🎉</h1>
  <p>Scratch the gray box below to reveal your prize!</p>
  <div class="container">
    <canvas id="scratchCanvas" width="300" height="150"></canvas>
  </div>
  <div class="message" id="message"></div>

<script>
  const canvas = document.getElementById("scratchCanvas");
  const ctx = canvas.getContext("2d");
  const width = canvas.width;
  const height = canvas.height;

  // List of rewards
  const prizes = [
    "📱 ဖုန်းဘေလ် 3000Ks",
    "💎 Diamond 11",
    "💎 Diamond 11",
    "💎 Diamond 11",
    "💎 Diamond 11",
    "💎 Diamond 11",
    "💎 Diamond 11",
    "🎁 Week Pack (1 week)",
    "💰 Cashback 10,000Ks",
    "🎉 Congratulations!",
    "🎉 Congratulations!",
    "🎉 Congratulations!",
    "🎉 Congratulations!",
    "🎉 Congratulations!"
  ];

  // Random prize
  const selectedPrize = prizes[Math.floor(Math.random() * prizes.length)];

  // Draw prize (hidden under scratch layer)
  ctx.fillStyle = "#ffffff";
  ctx.fillRect(0, 0, width, height);
  ctx.fillStyle = "#000000";
  ctx.font = "20px Arial";
  ctx.textAlign = "center";
  ctx.fillText(selectedPrize, width / 2, height / 2 + 10);

  const image = ctx.getImageData(0, 0, width, height);
  const scratchLayer = document.createElement("canvas");
  scratchLayer.width = width;
  scratchLayer.height = height;
  const scratchCtx = scratchLayer.getContext("2d");

  scratchCtx.fillStyle = "#aaa";
  scratchCtx.fillRect(0, 0, width, height);
  scratchCtx.globalCompositeOperation = "destination-out";

  const brushSize = 20;
  let isDrawing = false;

  const updateCanvas = (x, y) => {
    scratchCtx.beginPath();
    scratchCtx.arc(x, y, brushSize, 0, Math.PI * 2);
    scratchCtx.fill();
    draw();
  };

  const draw = () => {
    ctx.putImageData(image, 0, 0);
    ctx.drawImage(scratchLayer, 0, 0);
  };

  const getMouse = (e) => {
    const rect = canvas.getBoundingClientRect();
    return {
      x: (e.clientX || e.touches?.[0]?.clientX) - rect.left,
      y: (e.clientY || e.touches?.[0]?.clientY) - rect.top,
    };
  };

  const checkPercentage = () => {
    const pixels = scratchCtx.getImageData(0, 0, width, height).data;
    let cleared = 0;
    for (let i = 3; i < pixels.length; i += 4) {
      if (pixels[i] === 0) cleared++;
    }
    const percent = cleared / (width * height) * 100;
    if (percent > 50) {
      document.getElementById("message").innerText = "🎁 သင်သည် ဆုကြေးကိုအောင်မြင်စွာဖော်ထုတ်ပြီးပါပြီ!";
    }
  };

  canvas.addEventListener("mousedown", (e) => {
    isDrawing = true;
    const pos = getMouse(e);
    updateCanvas(pos.x, pos.y);
  });

  canvas.addEventListener("mousemove", (e) => {
    if (!isDrawing) return;
    const pos = getMouse(e);
    updateCanvas(pos.x, pos.y);
  });

  canvas.addEventListener("mouseup", () => {
    isDrawing = false;
    checkPercentage();
  });

  canvas.addEventListener("touchstart", (e) => {
    isDrawing = true;
    const pos = getMouse(e);
    updateCanvas(pos.x, pos.y);
  });

  canvas.addEventListener("touchmove", (e) => {
    if (!isDrawing) return;
    const pos = getMouse(e);
    updateCanvas(pos.x, pos.y);
    e.preventDefault();
  });

  canvas.addEventListener("touchend", () => {
    isDrawing = false;
    checkPercentage();
  });

  draw();
</script>
</body>
</html>
