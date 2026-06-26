<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Arañas Interactivas</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: black;
    }

    canvas {
      display: block;
    }
  </style>
</head>
<body>
  <canvas id="pantalla"></canvas>

  <script>
    const canvas = document.getElementById("pantalla");
    const ctx = canvas.getContext("2d");

    let ancho = window.innerWidth;
    let alto = window.innerHeight;

    canvas.width = ancho;
    canvas.height = alto;

    class Arania {
      constructor() {
        this.x = Math.random() * ancho;
        this.y = Math.random() * alto;

        this.destinoX = this.x;
        this.destinoY = this.y;

        this.offsetX = (Math.random() - 0.5) * 120;
        this.offsetY = (Math.random() - 0.5) * 120;

        this.patas = [];
        for (let i = 0; i < 8; i++) {
          this.patas.push({ x: this.x, y: this.y });
        }

        this.velocidad = 0.015 + Math.random() * 0.03;
      }

      mover(mouseX, mouseY) {
        this.destinoX = mouseX + this.offsetX;
        this.destinoY = mouseY + this.offsetY;
      }

      dibujar(tiempo) {
        this.x += (this.destinoX - this.x) * this.velocidad;
        this.y += (this.destinoY - this.y) * this.velocidad;

        ctx.beginPath();
        ctx.arc(this.x, this.y, 5, 0, Math.PI * 2);
        ctx.fill();

        for (let i = 0; i < this.patas.length; i++) {
          const pata = this.patas[i];

          const angulo =
            (i / 8) * Math.PI * 2 + Math.sin(tiempo * 0.004 + i) * 0.3;

          const largo = 35 + Math.sin(tiempo * 0.008 + i) * 8;

          const nuevaX = this.x + Math.cos(angulo) * largo;
          const nuevaY = this.y + Math.sin(angulo) * largo;

          pata.x += (nuevaX - pata.x) * 0.2;
          pata.y += (nuevaY - pata.y) * 0.2;

          ctx.beginPath();
          ctx.moveTo(this.x, this.y);
          ctx.lineTo((this.x + pata.x) / 2, (this.y + pata.y) / 2 - 8);
          ctx.lineTo(pata.x, pata.y);
          ctx.stroke();
        }
      }
    }

    const aranas = [];
    for (let i = 0; i < 8; i++) {
      aranas.push(new Arania());
    }

    window.addEventListener("pointermove", (evento) => {
      aranas.forEach((arania) => {
        arania.mover(evento.clientX, evento.clientY);
      });
    });

    function animacion(tiempo) {
      if (ancho !== window.innerWidth || alto !== window.innerHeight) {
        ancho = canvas.width = window.innerWidth;
        alto = canvas.height = window.innerHeight;
      }

      ctx.fillStyle = "rgba(0,0,0,0.25)";
      ctx.fillRect(0, 0, ancho, alto);

      ctx.fillStyle = "white";
      ctx.strokeStyle = "white";
      ctx.lineWidth = 2;
      ctx.lineCap = "round";
      ctx.lineJoin = "round";

      aranas.forEach((arania) => {
        arania.dibujar(tiempo);
      });

      requestAnimationFrame(animacion);
    }

    animacion();
  </script>
</body>
</html>

