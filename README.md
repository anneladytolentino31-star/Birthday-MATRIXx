<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Happy Birthday Matrix</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html, body {
      background-color: #000;
      color: #fff;
      font-family: Arial, sans-serif;
      overflow: hidden;
      touch-action: none;
      height: 100vh;
      width: 100vw;
      display: flex;
      justify-content: center;
      align-items: center;
      perspective: 1000px;
    }

    #bg-canvas {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
    }

    #content-layer {
      position: relative;
      z-index: 2;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      pointer-events: none;
    }

    /* Photo Cards */
    .photo-card {
      position: absolute;
      width: 320px;
      height: 320px;
      border-radius: 20px;
      overflow: hidden;
      border: 4px solid rgba(255, 182, 193, 0.9);
      box-shadow: 0 0 35px rgba(255, 105, 180, 0.6);
      background: #111;
      opacity: 0;
      transform: scale(0.8) rotate(-3deg);
      transition: all 0.8s ease-in-out;
      pointer-events: auto;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .photo-card img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .photo-card.active {
      opacity: 1;
      transform: scale(1) rotate(0deg);
    }

    /* Heart Collage Layout Container */
    #heart-container {
      position: absolute;
      width: 450px;
      height: 4
