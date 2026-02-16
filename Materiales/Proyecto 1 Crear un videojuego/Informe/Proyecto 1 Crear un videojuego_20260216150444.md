# Reporte de proyecto

## Estructura del proyecto

```
D:\Arcon\Clase\TAME\Programación multimedia y dispositivos móviles\000- Actividaddes\Proyecto 1 Crear un videojuego
├── 1.Juego cementerio (Frogger)
│   ├── Frogger.html
│   ├── ataud.png
│   ├── background.png
│   ├── corazon.png
│   ├── fantasma.png
│   └── spritesheet.png
├── 2.Juego particulas
│   └── 2.Juego particulas.html
├── 3.Asteroids
│   └── 3.Asteroids.html
├── 4.Simulador de particulas
│   └── 4.Simulador de particulas.html
├── 5.Aframe - bola del mundo
│   ├── 5.Aframe - bola del mundo.html
│   ├── nasanubes.png
│   ├── nasatierra.jpg
│   └── rugosidad.jpg
├── 6.CSS 3d - cards
│   └── Cards 3d.html
├── 7.Isometrico
│   ├── Isometrico.html
│   └── spritesheet.png
├── 8.Minecraft
│   └── 8.Minecraft.html
└── Informe
```

## Código (intercalado)

# Proyecto 1 Crear un videojuego
## 1.Juego cementerio (Frogger)
**Frogger.html**
```html
<!DOCTYPE html>
<html>
    <head>
        <style>
            body, html{
                margin: 0;
                padding: 0;
                overflow: hidden;
                background: #111;
            }
            canvas {
                display: block;
                margin: auto;
                background: #222;
            }
        </style>
    </head>
    <body>
        <canvas width="512" height="512"></canvas>
        <script>
            const lienzo=document.querySelector("canvas");
            const ctx=lienzo.getContext("2d");
            const anchura=512;
            const altura=512;
            const tamBloque=64;

            //Sprites
            const fondo = new Image();
            fondo.src= "background.png";
            const spriteSkeleton = new Image();
            spriteSkeleton.src= "spritesheet.png";
            const spriteGhost = new Image();
            spriteGhost.src= "fantasma.png";
            const corazon = new Image();
            corazon.src= "corazon.png";
            const ataud = new Image();
            ataud.src= "ataud.png";

            //Variables
            let nivel=1;
            let vidas=3;
            let juegoTerminado=false;
            let mensaje="";

            //Clase
            class Esqueleto{
                constructor(x, y, ancho, alto, sprite) {
                    this.x=x;
                    this.y=y;
                    this.ancho=ancho;
                    this.alto=alto;
                    this.frame=0;
                    this.dir=0;
                    this.sprite=sprite;
                }

                dibujar(ctx) {
                    ctx.drawImage(
                        this.sprite,
                        this.frame*tamBloque,
                        this.dir*tamBloque,
                        tamBloque,tamBloque,
                        this.x,this.y,
                        this.ancho,this.alto
                    );
                }

                animar() {
                    this.frame=(this.frame+1)%4;
                }

                mover(direccion){
                    this.animar();
                    switch (direccion){
                        case "arriba":
                        this.y -= tamBloque/2;
                        this.dir= 0;
                        break;
                        case "abajo":
                        this.y += tamBloque/2;
                        this.dir= 2;
                        break;
                        case "izquierda":
                        this.x -= tamBloque/2;
                        this.dir= 1;
                        break;
                        case "derecha":
                        this.x += tamBloque/2;
                        this.dir= 3;
                        break;
                    }

                    this.x = Math.max(0, Math.min(this.x, anchura-this.ancho));
                    this.y = Math.max(0, Math.min(this.y, altura-this.alto));
                }

                reiniciar(){
                    this.x= anchura/2-this.ancho/2;
                    this.y= altura-this.alto-5;
                }
            }

            const esqueleto = new Esqueleto(
                anchura/2-tamBloque/2,
                altura-tamBloque-5,
                tamBloque,
                tamBloque,
                spriteSkeleton
            );

            //Fantasmas
            function crearFantasmas(mult){
                return [
                {x: 0, y: 400, w: 64, h: 48, vel: 1.6* mult},
                {x: 250, y: 290, w: 64, h: 48, vel: -1.8* mult},
                { x: 80, y: 180, w: 64, h: 48, vel: 1.7* mult},
                {x: 200, y: 70, w: 64, h: 48, vel: -1.5* mult}
                ];
            }
            let fantasmas = crearFantasmas(1);

            const meta = { x: anchura / 2 - 40, y: 15, ancho: 80, alto: 80 };


            //Colision
            function colision(a, b){
                const margenX = b.w * 0.25;
                const margenY = b.h * 0.25;
                const bx = b.x + margenX;
                const by = b.y + margenY;
                const bw = b.w - margenX * 2;
                const bh = b.h - margenY * 2;
                return (
                a.x < bx + bw &&
                a.x + a.ancho > bx &&
                a.y < by + bh &&
                a.y + a.alto > by
                );
            }

            //Dibuja
            function dibuja(){
                ctx.clearRect(0, 0, anchura, altura);
                ctx.drawImage(fondo, 0, 0, anchura, altura);
                ctx.drawImage(ataud, meta.x, meta.y, meta.ancho, meta.alto);

                fantasmas.forEach(f => ctx.drawImage(spriteGhost, f.x, f.y, f.w, f.h));
                esqueleto.dibujar(ctx);

                ctx.fillStyle = "white";
                ctx.font = "18px Arial";
                ctx.fillText(`Nivel: ${nivel}`, 20, 30);
                for (let i = 0; i < vidas; i++) {
                ctx.drawImage(corazon, anchura - 40 - i * 40, 10, 30, 30);
                }

                if (juegoTerminado){
                const titulo = mensaje;
                const subtitulo = "Pulsa ENTER para continuar";
                ctx.font = "24px Arial";
                const wTitulo = ctx.measureText(titulo).width;
                ctx.font = "16px Arial";
                const wSub = ctx.measureText(subtitulo).width;
                const paddingX = 20, paddingY = 12;
                const rectW = Math.max(wTitulo, wSub) + paddingX * 2;
                const rectH = 24 + 16 + paddingY * 2 + 6;
                const rectX = (anchura - rectW) / 2;
                const rectY = (altura - rectH) / 2 - 10;

                ctx.fillStyle = "rgba(0,0,0,0.7)";
                ctx.fillRect(rectX, rectY, rectW, rectH);
                ctx.fillStyle = "white";
                ctx.font = "24px Arial";
                ctx.fillText(titulo, anchura / 2 - wTitulo / 2, rectY + paddingY + 20);
                ctx.font = "16px Arial";
                ctx.fillText(subtitulo, anchura / 2 - wSub / 2, rectY + paddingY + 44);
                }
            }

            //Actualizar
            function actualiza(){
                if (juegoTerminado) return;

                fantasmas.forEach(f => {
                f.x += f.vel;
                if (f.vel > 0 && f.x > anchura + 80) f.x = -80;
                if (f.vel < 0 && f.x < -80) f.x = anchura + 80;

                if (colision(esqueleto, f)) {
                    vidas--;
                    if (vidas <= 0) {
                    mensaje = "💀 Has perdido todas tus vidas";
                    juegoTerminado = true;
                    nivel = 1;
                    } else {
                    mensaje = `☠️ Te atrapó un fantasma ${vidas} vidas restantes`;
                    juegoTerminado = true;
                    }
                }
                });

                const margenMetaY = meta.alto * 0.6;
                if (
                esqueleto.x + esqueleto.ancho / 2 > meta.x &&
                esqueleto.x + esqueleto.ancho / 2 < meta.x + meta.ancho &&
                esqueleto.y < meta.y + margenMetaY
                ) {
                mensaje = "🎉 ¡Nivel completado!";
                juegoTerminado = true;
                if (vidas < 3) vidas++;
                }
            }

            //Movimiento
            document.body.onkeydown = function (e){
                if (juegoTerminado && e.key === "Enter") {
                if (mensaje.includes("Nivel completado")) nivel++;
                else if (mensaje.includes("perdido todas")) { vidas = 3; nivel = 1; }
                iniciarNivel();
                return;
                }
                if (juegoTerminado) return;

                switch (e.key){
                case "w":
                case "ArrowUp": 
                esqueleto.mover("arriba");
                break;
                case "s":
                case "ArrowDown":
                esqueleto.mover("abajo");
                break;
                case "a":
                case "ArrowLeft": 
                esqueleto.mover("izquierda");
                break;
                case "d":
                case "ArrowRight": 
                esqueleto.mover("derecha");
                break;
                }
            };

            //Subir nivel
            function iniciarNivel(){
                esqueleto.reiniciar();
                fantasmas = crearFantasmas(1+(nivel-1)*0.3);
                juegoTerminado = false;
                mensaje = "";
            }

            //Bucle
            function bucle(){
                actualiza();
                dibuja();
                requestAnimationFrame(bucle);
            }

            bucle();

        </script>
    </body>
</html>
```
## 2.Juego particulas
**2.Juego particulas.html**
```html
<!DOCTYPE html>
<html>
    <head>
        <style>canvas{border: 1px solid grey;}html,body{padding: 0px; margin:0px; overflow: hidden;} </style>
    </head>
    <body>
        <canvas width="512" height="512"></canvas>
        <script>
            const anchuraventana = window.innerWidth
            const alturaventana = window.innerHeight
            const lienzo = document.querySelector("canvas");
            lienzo.width = anchuraventana
            lienzo.height = alturaventana
            const contexto = lienzo.getContext("2d");

            

            class Particula{

                constructor(){
                    this.posx = Math.random()*anchuraventana
                    this.posy = Math.random()*alturaventana
                    this.direccion = Math.random()*Math.PI*2
                    this.rojo = Math.round(Math.random()*255)
                    this.verde = Math.round(Math.random()*255)
                    this.azul = Math.round(Math.random()*255)
                    this.velocidad = Math.random()*0.5;
                    this.tamanio = Math.random()*10+1;
                }

                dibuja() {
                    contexto.fillStyle= "rgb("+this.rojo+","+this.verde+","+this.azul+")"
                    contexto.beginPath();
                    contexto.arc(this.posx,this.posy,this.tamanio,0,Math.PI*2)
                    contexto.fill()
                }
                mueve(){
                    this.direccion += (Math.random()-0.5)*0.5
                    this.posx += Math.cos(this.direccion)*this.velocidad
                    this.posy += Math.sin(this.direccion)*this.velocidad
                }

                colision(){
                    if(
                        this.posx < 0 ||
                        this.posx > anchuraventana ||
                        this.posy < 0 ||
                        this.posy > alturaventana){
                            this.direccion += Math.PI
                        }
                }

                getPosicion(){
                    return{"x":this.posx,"y":this.posy}
                }
            }

            var particulas = []
            var numeropartuclas = 500;
            for(let i = 0; i<numeropartuclas; i++){
                particulas.push(new Particula())
            }

            var temporizador = setTimeout("bucle()",1000)

            function bucle(){
                contexto.fillStyle= "rgba(255,255,255,0.1)"
                contexto.fillRect(0,0,anchuraventana,alturaventana)
                
                particulas.forEach(function(particula){
                    particula.colision();
                    particula.mueve();
                    particula.dibuja();

                })

                for(let i = 0; i<particulas.length; i++){
                    for(let j = 0; j<particulas.length; j++){
                        if(i != j){
                            if(
                                Math.abs(particulas[i].getPosicion().x - particulas[j].getPosicion().x)<10
                                &&
                                Math.abs(particulas[i].getPosicion().y - particulas[j].getPosicion().y)<10
                            ){
                            particulas[i].direccion += Math.PI
                            particulas[j].direccion += Math.PI
                            }
                            if(
                                Math.abs(particulas[i].getPosicion().x - particulas[j].getPosicion().x)<50
                                &&
                                Math.abs(particulas[i].getPosicion().y - particulas[j].getPosicion().y)<50
                            ){
                            contexto.strokeStyle= "rgb(127,127,127,0.1)"
                            contexto.beginPath()
                            contexto.moveTo(
                                particulas[i].getPosicion().x,
                                particulas[i].getPosicion().y)
                                contexto.lineTo(
                                    particulas[j].getPosicion().x,
                                    particulas[j].getPosicion().y)
                                    contexto.stroke()
                            }
                            
                        }
                    
                    }
                }

                //Vuelvo a ejecutar
                clearTimeout(temporizador)
                temporizador = setTimeout("bucle()",10)
            }

            //Ahira interactuo con el proyecto
            lienzo.onclick = function(event){
                particulas.push(new Particula())
                particulas[particulas.length-1].posx = event.x
                particulas[particulas.length-1].posy = event.y
            }
            lienzo.onmousemove = function(event){
                particulas.forEach(function(particula){
                    if(
                        Math.abs(event.x - particula.posx) < 100 &&
                        Math.abs(event.y - particula.posy) < 100)
                        {
                       angulo = angleBetweenPoints (event.x, event.y , particula.posx, particula.posy)

                        particula.direccion = angulo.radians
                        particula.posx += Math.cos(particula.direccion)*1
                        particula.posy += Math.sin(particula.direccion)*1
                    }
                })
            }

            function angleBetweenPoints (x1, y1 , x2, y2) {
                let dx = x2 - x1;
                let dy = y2 - y1;

                let angleRadians = Math.atan2(dy,dx);

                let angleDegrees = angleRadians * (180 / Math.PI);

                return{
                    radians: angleRadians,
                    degrees: angleDegrees
                };
            }
            

        </script>
    </body>
</html>
```
## 3.Asteroids
**3.Asteroids.html**
```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Asteroids</title>
    <style>
      html,body{margin:0;padding:0;overflow:hidden;background:#000}
      canvas{display:block}
    </style>
  </head>
  <body>
    <canvas></canvas>
    <script>
      // ========== Utilidades ==========
      function distancia(x1, y1, x2, y2) {
        const dx = x2 - x1, dy = y2 - y1;
        return Math.hypot(dx, dy);
      }
      function rand(min, max){ return Math.random()*(max-min)+min }

      // ========== Clases ==========
      class Jugador{
        constructor(){
          this.posx = anchura/2;
          this.posy = altura/2;
          this.angulo = 0;
          this.velx = 0;
          this.vely = 0;
          this.aceleracion = 0.18;
          this.rozamiento = 0.995;
          this.velMax = 8;
        }
        dibuja(){
          // Triángulo
          const noseLen = 22, baseLen = 14, spread = Math.PI*0.75;
          const noseX = this.posx + Math.cos(this.angulo)*noseLen;
          const noseY = this.posy + Math.sin(this.angulo)*noseLen;
          const leftX = this.posx + Math.cos(this.angulo + spread)*baseLen;
          const leftY = this.posy + Math.sin(this.angulo + spread)*baseLen;
          const rightX= this.posx + Math.cos(this.angulo - spread)*baseLen;
          const rightY= this.posy + Math.sin(this.angulo - spread)*baseLen;

          contexto.fillStyle = "white";
          contexto.beginPath();
          contexto.moveTo(noseX,noseY);
          contexto.lineTo(leftX,leftY);
          contexto.lineTo(rightX,rightY);
          contexto.closePath();
          contexto.fill();

          // Punto rojo
          contexto.fillStyle = "red";
          contexto.beginPath();
          contexto.arc(this.posx,this.posy,5,0,Math.PI*2);
          contexto.fill();
        }
        aplicaThrust(on){
          if(!on) return;
          this.velx += Math.cos(this.angulo)*this.aceleracion;
          this.vely += Math.sin(this.angulo)*this.aceleracion;
          const v = Math.hypot(this.velx, this.vely);
          if(v > this.velMax){
            const f = this.velMax / v;
            this.velx *= f; this.vely *= f;
          }
        }
        mueve(){
          this.velx *= this.rozamiento;
          this.vely *= this.rozamiento;
          this.posx += this.velx;
          this.posy += this.vely;

          // Mantenemos wrap para el jugador
          if (this.posx < 0) this.posx += anchura;
          if (this.posx > anchura) this.posx -= anchura;
          if (this.posy < 0) this.posy += altura;
          if (this.posy > altura) this.posy -= altura;
        }
      }

      class Bala{
        constructor(x,y,a){
          this.posx = x; this.posy = y; this.angulo = a;
          this.velocidad = 12;
        }
        dibuja(){
          contexto.fillStyle = "dodgerblue";
          contexto.beginPath();
          contexto.arc(this.posx,this.posy,3,0,Math.PI*2);
          contexto.fill();
        }
        mueve(){
          this.posx += Math.cos(this.angulo)*this.velocidad;
          this.posy += Math.sin(this.angulo)*this.velocidad;
        }
      }

      class Estrella{
        constructor(){
          this.posx = Math.random()*anchura;
          this.posy = Math.random()*altura;
        }
        dibuja(){
          contexto.fillStyle = "white";
          contexto.beginPath();
          contexto.arc(this.posx,this.posy,1,0,Math.PI*2);
          contexto.fill();
        }
      }

      class Roca{
        constructor(){
          this.radio = Math.random()*20+10;
          // Evitar spawnear demasiado cerca del jugador
          let ok = false;
          while(!ok){
            this.posx = Math.random()*anchura;
            this.posy = Math.random()*altura;
            ok = distancia(this.posx,this.posy,jugador?.posx||anchura/2,jugador?.posy||altura/2) > 80;
          }
          this.angulo = Math.random()*Math.PI*2;
          this.lados = Math.round(Math.random()*20+5);
          const rugosidad = 0.4;
          this.puntas = Array.from({length:this.lados}, () => 1 + (Math.random()*2 - 1) * rugosidad);
          // Velocidad inicial
          const v = Math.random()*1.6+0.4;
          const dir = Math.random()*Math.PI*2;
          this.vx = Math.cos(dir)*v;
          this.vy = Math.sin(dir)*v;
          this.rot = (Math.random()-0.5)*0.04;
        }
        dibuja(){
          contexto.fillStyle = "grey";
          contexto.beginPath();
          for(let i=0;i<this.lados;i++){
            const ang = (i/this.lados)*Math.PI*2 + this.angulo;
            const r = this.radio * this.puntas[i];
            const x = this.posx + Math.cos(ang)*r;
            const y = this.posy + Math.sin(ang)*r;
            if(i===0) contexto.moveTo(x,y); else contexto.lineTo(x,y);
          }
          contexto.closePath();
          contexto.strokeStyle = "#333";
          contexto.fill();
        }
        mueve(){
          this.angulo += this.rot;
          this.posx += this.vx;
          this.posy += this.vy;

          // Ricochet en bordes (considerando el radio)
          if (this.posx - this.radio < 0){
            this.posx = this.radio;
            this.vx = -this.vx;
          } else if (this.posx + this.radio > anchura){
            this.posx = anchura - this.radio;
            this.vx = -this.vx;
          }
          if (this.posy - this.radio < 0){
            this.posy = this.radio;
            this.vy = -this.vy;
          } else if (this.posy + this.radio > altura){
            this.posy = altura - this.radio;
            this.vy = -this.vy;
          }
        }
      }

      // ========== Setup ==========
      const anchura = window.innerWidth;
      const altura  = window.innerHeight;
      const lienzo = document.querySelector("canvas");
      lienzo.width = anchura; lienzo.height = altura;
      const contexto = lienzo.getContext("2d");

      const jugador = new Jugador();

      let estrellas = Array.from({length:100}, ()=>new Estrella());
      let balas = [];

      // Niveles
      let level = 1;
      let rocksPerLevel = 10; // base
      let rocas = [];
      let levelMessageTimer = 0; // frames restantes para mostrar “LEVEL N”

      function spawnRocas(n){
        for(let i=0;i<n;i++) rocas.push(new Roca());
      }
      function startLevel(){
        rocas.length = 0;
        spawnRocas(rocksPerLevel);
        levelMessageTimer = 120; // ~2s a 60fps
      }
      startLevel();

      // ========== Controles ==========
      let giro = 0;       // -1 izq, 1 der
      let thrust = false; // W

      document.body.onkeydown = (e)=>{
        switch(e.key){
          case "a": giro = -1; break;
          case "d": giro = 1;  break;
          case "w": thrust = true; break;
        }
        if(e.code === "Space"){
          balas.push(new Bala(jugador.posx, jugador.posy, jugador.angulo));
        }
      };
      document.body.onkeyup = (e)=>{
        switch(e.key){
          case "a": if(giro === -1) giro = 0; break;
          case "d": if(giro === 1)  giro = 0; break;
          case "w": thrust = false; break;
        }
      };

      // ========== Bucle ==========
      let temporizador = null;
      function drawLevelText(){
        if(levelMessageTimer <= 0) return;
        contexto.save();
        contexto.font = "bold 48px sans-serif";
        contexto.textAlign = "center";
        contexto.textBaseline = "middle";
        contexto.fillStyle = "white";
        contexto.strokeStyle = "rgba(0,0,0,0.6)";
        contexto.lineWidth = 6;
        const msg = `LEVEL ${level}`;
        contexto.strokeText(msg, anchura/2, altura*0.2);
        contexto.fillText(msg, anchura/2, altura*0.2);
        contexto.restore();
        levelMessageTimer--;
      }

      function bucle(){
        // Input
        jugador.angulo += giro * 0.08;
        jugador.aplicaThrust(thrust);

        // Fondo
        contexto.fillStyle = "black";
        contexto.fillRect(0,0,anchura,altura);

        // Estrellas
        estrellas.forEach(e=>e.dibuja());

        // Entidades
        rocas.forEach(r=>{ r.dibuja(); r.mueve(); });
        balas.forEach(b=>{ b.dibuja(); b.mueve(); });

        // Jugador
        jugador.mueve();
        jugador.dibuja();

        // Colisiones bala-roca
        for(let i=rocas.length-1;i>=0;i--){
          const roca = rocas[i];
          for(let j=balas.length-1;j>=0;j--){
            const bala = balas[j];
            if(distancia(bala.posx,bala.posy,roca.posx,roca.posy) < roca.radio){
              rocas.splice(i,1);
              balas.splice(j,1);
              break;
            }
          }
        }

        // Borrar balas fuera
        for(let j=balas.length-1;j>=0;j--){
          if(balas[j].posx<0 || balas[j].posx>anchura || balas[j].posy<0 || balas[j].posy>altura){
            balas.splice(j,1);
          }
        }

        // ¿Nivel completado?
        if(rocas.length === 0){
          level++;
          rocksPerLevel *= 2; // duplicar
          startLevel();
        }

        // HUD nivel (esquina)
        contexto.fillStyle = "white";
        contexto.font = "16px monospace";
        contexto.fillText(`Level: ${level}`, 12, 22);

        // Cartel de inicio de nivel
        drawLevelText();

        temporizador = setTimeout(bucle, 16); // ~60fps
      }
      temporizador = setTimeout(bucle, 16);
    </script>
  </body>
</html>
```
## 4.Simulador de particulas
**4.Simulador de particulas.html**
```html
<!DOCTYPE html>
<html>
    <head>

    </head>
    <body>
        Angulo<input type="range" id="angulo" min=0 max=6.283185307 step=0.01><br>
        Radio<input type="range" id="radio" min=1 max=50 step=0.01><br>
        Velocidad<input type="range" id="velocidad" min=0 max=10><br>
        Amplitud<input type="range" id="amplitud" min=0 max=2 step=0.01><br>
        Color<input type="color" id="color"><br>
        <canvas id="lienzo" width=512 height=512></canvas>
        <script>
            class Particula{
                constructor(x,y,v,a,color){
                    this.x = x;
                    this.y = y;
                    this.v = v;
                    this.a = a;
                    this.color= color
                }
                dibuja(){
                    contexto.fillStyle = this.color
                    contexto.fillRect(this.x,this.y,1,1)
                }
                mueve(){
                    this.x += Math.cos(this.a)*this.v
                    this.y += Math.sin(this.a)*this.v
                }
                
            }
            class Emisor{
                constructor(x,y,a,v,apertura,color,radio){
                    this.x = x;
                    this.y = y;
                    this.a = a;
                    this.v = v;
                    this.apertura = apertura
                    this.color = color
                    this.radio = radio
                }
            }

            function previsualizacion(){
                //Pinta previsualizacion
                contexto.strokeStyle = document.querySelector("#color").value
                contexto.beginPath()
                contexto.moveTo(anchura/2,altura/2)
                contexto.lineTo(
                    anchura/2 + Math.cos(document.querySelector("#angulo").value-document.querySelector("#amplitud").value/2)*document.querySelector("#velocidad").value*5
                    ,altura/2 + Math.sin(document.querySelector("#angulo").value-document.querySelector("#amplitud").value/2)*document.querySelector("#velocidad").value*5
                )
                contexto.stroke()
                contexto.beginPath()
                contexto.moveTo(anchura/2,altura/2)
                contexto.lineTo(
                    anchura/2 + Math.cos(document.querySelector("#angulo").value+document.querySelector("#amplitud").value/2)*document.querySelector("#velocidad").value*5
                    ,altura/2 + Math.sin(document.querySelector("#angulo").value+document.querySelector("#amplitud").value/2)*document.querySelector("#velocidad").value*5
                )
                contexto.stroke()
                contexto.beginPath()
                contexto.arc(
                    256,
                    256,
                    document.querySelector("#radio").value*1,
                    0,
                    Math.PI*2
                )
                contexto.stroke()
            }

            //Función actualizar emisores en tiempo real
            function actualizaParametros() {
                emisores.forEach(emisor => {
                    emisor.a = parseFloat(document.querySelector("#angulo").value);
                    emisor.v = parseFloat(document.querySelector("#velocidad").value);
                    emisor.apertura = parseFloat(document.querySelector("#amplitud").value);
                    emisor.color = document.querySelector("#color").value;
                    emisor.radio = parseFloat(document.querySelector("#radio").value);
                })
            }


            //Variables globales
            var lienzo = document.querySelector("canvas")
            var contexto = lienzo.getContext("2d")
            var temporizador;
            var numparticulas = 50
            var particulas = []
            var numemisores = 1
            var emisores = []
            var blancotransparente = "rgba(255,255,255,0.4)";
            var anchura = 512
            var altura = 512;
            
            inicio();

            function inicio(){
                lienzo.onclick = function(event){
                    emisores.push(new Emisor(
                        event.offsetX,
                        event.offsetY,
                        parseFloat(document.querySelector("#angulo").value*1),
                        parseFloat(document.querySelector("#velocidad").value*1),
                        parseFloat(document.querySelector("#amplitud").value*1),
                        document.querySelector("#color").value,
                        parseFloat(document.querySelector("#radio").value*1)
                    ))

                }
                document.querySelectorAll("#angulo, #velocidad, #amplitud, #color, #radio")
                    .forEach(input => input.addEventListener("input", actualizaParametros))

                temporizador = setTimeout("bucle()",1000)
            }
            function bucle(){
                emisores.forEach(function(emisor){
                    for(let i=0;i<numparticulas; i++){
                    particulas.push(
                        new Particula(
                            emisor.x+Math.cos(Math.random()*Math.PI*2)*emisor.radio,
                            emisor.y+Math.sin(Math.random()*Math.PI*2)*emisor.radio,
                            emisor.v,
                            emisor.a+ (Math.random()-0.5)*emisores[0].apertura*1,
                            emisor.color
                        )
                    )}
                })

                contexto.fillStyle = blancotransparente
                contexto.fillRect(0,0,512,512)

                

                previsualizacion()

                // Pinto una previsualizacion
                particulas.forEach(function(particula){
                    particula.mueve()
                    particula.dibuja()
                })
                //Eliminamos particulares
                for(let i = 0; i<particulas.length;i++){
                    if(
                        particulas[i].x < 0||
                        particulas[i].x > 512||
                        particulas[i].y < 0||
                        particulas[i].y > 512
                    ){
                        particulas.splice(i,1) //Elimina del array las particulas de fuera de la pantalla
                    }
                }
                
                clearTimeout(temporizador)
                temporizador = setTimeout("bucle()",10)
            }
        </script>
    </body>
</html>
```
## 5.Aframe - bola del mundo
**5.Aframe - bola del mundo.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <script src="https://cdn.jsdelivr.net/gh/akbartus/A-Frame-Component-Postprocessing/dist/aframe.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/akbartus/A-Frame-Component-Postprocessing/dist/post-processing.min.js"></script>

    <style>
      html, body{ margin: 0; height: 100%; background: #000; }
    </style>
  </head>
  <body>
    <a-scene
      post-processing="effect: bloom; bloomParams: threshold: 0.18, strength: 0.35, radius: 0.55, exposure: 1.0"
      renderer="colorManagement: true; physicallyCorrectLights: true;"
    >
      <a-assets>
        <img id="texturatierra" src="nasatierra.jpg" crossorigin="anonymous">
        <img id="rugosidadtierra" src="rugosidad.jpg" crossorigin="anonymous">
        <img id="nubes" src="nasanubes.png" crossorigin="anonymous">
      </a-assets>

      <a-entity
        id="earthGroup"
        position="0 1.25 -5"
        animation="property: rotation; to: 0 360 0; loop: true; dur: 130000; easing: linear"
      >
        <a-sphere
          radius="3"
          material="src: #texturatierra; metalness: 0.01; roughness: 0.8; roughnessMap: #rugosidadtierra"
        ></a-sphere>

        <a-sphere
          radius="3.05"
          segments-width="64"
          segments-height="64"
          material=
            "src: #nubes;
            alphaMap: #nubes;
            transparent: true;
            side: double;
            metalness: 0;
            roughness: 1;
            emissive: #ffffff;
            emissiveIntensity: 0.25;
            emissiveMap: #nubes;"
        ></a-sphere>
      </a-entity>

      <a-sky color="#000000"></a-sky>

      <a-entity light="type: ambient; intensity: 0.15"></a-entity>

      <a-entity light="type: directional; intensity: 2.5; color: #ffffff" position="2 4 0"></a-entity>

      <a-entity position="0 1.6 0">
        <a-camera></a-camera>
      </a-entity>
    </a-scene>
  </body>
</html>
```
## 6.CSS 3d - cards
**Cards 3d.html**
```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>CSS3D Portfolio • Parallax + Layered Depth</title>
<style>
  :root{
    --bg: #0b0f14;
    --card: #0f1720;
    --ink: #e6eef9;
    --muted: #a9b6c7;
    --ring: #4da3ff;
    --gap: 24px;
    --pad: 32px;
    --radius: 18px;
    --perspective: 1400px;

    /* Camera rotations (set by JS) */
    --rx: 0deg;
    --ry: 0deg;

    /* Mouse normalized position (set by JS, -1..1) */
    --mx: 0;
    --my: 0;

    /* Slight zoom-out to keep everything inside viewport while rotating */
    --stageScale: .96;
  }

  html, body { height: 100%; }
  body{
    margin:0; color:var(--ink); overflow:hidden;
    background:
      radial-gradient(1200px 1200px at 80% -20%, #15314d 0%, #0b0f14 60%) fixed;
    font-family: system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, "Helvetica Neue", Arial, "Noto Sans";
  }

  /* Scene gives us perspective (camera) */
  .scene{
    height: 100vh; padding: var(--pad); box-sizing: border-box;
    perspective: var(--perspective);
    perspective-origin: 50% 75%;
    display: grid; grid-template-rows: auto 1fr; gap: var(--gap);
  }

  header{ display:flex; align-items:baseline; justify-content:space-between; }
  h1{ margin:0; font-weight:700; letter-spacing:.2px; font-size: clamp(18px, 2.2vw, 32px); }
  .subtitle{ color:var(--muted); font-size: clamp(12px, 1.2vw, 16px); }

  /* Stage holds everything that rotates in 3D */
  .stage{
    position: relative; width:100%; height:100%;
    transform-style: preserve-3d;
    transform:
      translateZ(0)
      rotateX(var(--rx))
      rotateY(var(--ry))
      scale(var(--stageScale));
    transform-origin: 50% 80%;
    will-change: transform;
  }

  /* BACKGROUND DEPTH LAYERS (SVG masks punch real transparent holes) */
  .depth-layer{
    position:absolute; inset:0;
    transform-style: preserve-3d;
    pointer-events:none;
    will-change: transform;
    /* Each layer uses its own --z, --px, --py (set inline) */
    transform:
      translate3d(
        calc(var(--mx) * var(--px)),
        calc(var(--my) * var(--py)),
        var(--z)
      )
      rotateX(0deg) rotateY(0deg) scale(3,3);
  }

  /* A soft floor shadow to sell depth */
  .floor-shadow{
    position:absolute; inset:-6% -10% -25% -10%;
    background:
      radial-gradient(120% 35% at 50% 100%,
        rgba(0,0,0,.42) 0%,
        rgba(0,0,0,.24) 35%,
        rgba(0,0,0,0) 70%);
    transform: translateZ(-220px) rotateX(90deg);
    pointer-events:none;
  }

  /* GRID */
  .grid{
    position:relative; width:100%; height:100%;
    display:grid; gap: var(--gap);
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: repeat(3, 1fr);
    transform-style: preserve-3d;
  }

  /* CARDS */
  .card{
    position:relative; border-radius: var(--radius); overflow:hidden;
    background: var(--card);
    box-shadow:
      0 1px 0 rgba(255,255,255,0.05) inset,
      0 10px 30px rgba(0,0,0,0.35);
    transform-style: preserve-3d;
    transition:
      transform .25s ease,
      box-shadow .25s ease,
      filter .25s ease;
    will-change: transform, box-shadow, filter;
    --dz: 0px;               /* depth from pointer proximity (JS) */
    transform: translateZ(var(--dz));
  }
  .card:hover{
    transform:
      translateZ(calc(var(--dz) + 70px))
      rotateX(-2deg)
      rotateY(2deg);
    box-shadow:
      0 1px 0 rgba(255,255,255,0.06) inset,
      0 22px 60px rgba(0,0,0,0.5);
    filter: saturate(1.08);
  }

  .thumb{
    position:absolute; inset:0;
    background:
      linear-gradient(to bottom right, rgba(255,255,255,0.06), rgba(255,255,255,0.0)),
      repeating-linear-gradient(135deg, rgba(255,255,255,0.06) 0 2px, transparent 2px 6px),
      radial-gradient(120% 120% at 0% 0%, #204a72 0%, #132336 45%, #0f1720 80%);
    transform: translateZ(1px);
  }
  .veil{
    position:absolute; inset:0;
    background: linear-gradient(to top, rgba(0,0,0,.55) 0 35%, rgba(0,0,0,0) 60%);
    transform: translateZ(2px);
    pointer-events:none;
  }
  .meta{
    position:absolute; left:16px; right:16px; bottom:14px;
    display:flex; align-items:center; justify-content:space-between; gap:12px;
    transform: translateZ(8px);
  }
  .title{
    font-weight:650; font-size: clamp(12px, 1.1vw, 18px);
    text-shadow: 0 1px 2px rgba(0,0,0,.6);
    white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
  }
  .tag{
    font-size: clamp(10px, .9vw, 13px);
    padding: 6px 10px; border-radius: 999px;
    background: rgba(77,163,255,.14); color: #cfe5ff;
    border: 1px solid rgba(77,163,255,.25);
    backdrop-filter: blur(4px);
  }
  .card:focus-visible{ outline: 3px solid var(--ring); outline-offset: 3px; }

  @media (max-aspect-ratio: 16/10), (max-width: 1200px){
    :root{ --gap:16px; --pad:16px; --perspective: 1100px; }
  }
</style>
</head>
<body>
  <div class="scene" id="scene">
    <header>
      <h1>CSS3D Parallax Portfolio</h1>
      <div class="subtitle">Move mouse: camera parallax • Hover: 3D pop • Layered depth background</div>
    </header>

    <div class="stage" id="stage">
      <!-- Depth layers (SVG masks create transparent “holes”) -->
      <!-- FAR layer -->
      <svg class="depth-layer" style="--z:-220px; --px:16px; --py:22px" viewBox="0 0 1920 1080" preserveAspectRatio="none" aria-hidden="true">
        <defs>
          <mask id="mask-far">
            <rect x="0" y="0" width="1920" height="1080" fill="white"/>
            <!-- holes (black = transparent in mask) -->
            <circle cx="200" cy="180" r="80" fill="black"/>
            <circle cx="520" cy="260" r="55" fill="black"/>
            <circle cx="860" cy="160" r="70" fill="black"/>
            <circle cx="1280" cy="230" r="85" fill="black"/>
            <circle cx="1650" cy="160" r="65" fill="black"/>
            <circle cx="320" cy="520" r="95" fill="black"/>
            <circle cx="720" cy="620" r="60" fill="black"/>
            <circle cx="1120" cy="560" r="75" fill="black"/>
            <circle cx="1520" cy="580" r="90" fill="black"/>
            <circle cx="420" cy="900" r="80" fill="black"/>
            <circle cx="980" cy="880" r="65" fill="black"/>
            <circle cx="1500" cy="880" r="78" fill="black"/>
          </mask>
        </defs>
        <rect x="0" y="0" width="1920" height="1080"
              fill="rgba(77,163,255,0.10)" mask="url(#mask-far)"/>
      </svg>

      <!-- MID layer -->
      <svg class="depth-layer" style="--z:-180px; --px:28px; --py:36px" viewBox="0 0 1920 1080" preserveAspectRatio="none" aria-hidden="true">
        <defs>
          <mask id="mask-mid">
            <rect x="0" y="0" width="1920" height="1080" fill="white"/>
            <circle cx="160" cy="140" r="70" fill="black"/>
            <circle cx="460" cy="200" r="42" fill="black"/>
            <circle cx="780" cy="140" r="58" fill="black"/>
            <circle cx="1180" cy="210" r="72" fill="black"/>
            <circle cx="1700" cy="180" r="52" fill="black"/>
            <circle cx="260" cy="500" r="75" fill="black"/>
            <circle cx="640" cy="640" r="48" fill="black"/>
            <circle cx="1010" cy="560" r="62" fill="black"/>
            <circle cx="1400" cy="620" r="78" fill="black"/>
            <circle cx="360" cy="900" r="72" fill="black"/>
            <circle cx="920" cy="900" r="54" fill="black"/>
            <circle cx="1440" cy="880" r="66" fill="black"/>
          </mask>
        </defs>
        <rect x="0" y="0" width="1920" height="1080"
              fill="rgba(77,163,255,0.14)" mask="url(#mask-mid)"/>
      </svg>

      <!-- NEAR layer -->
      <svg class="depth-layer" style="--z:-40px; --px:44px; --py:54px" viewBox="0 0 1920 1080" preserveAspectRatio="none" aria-hidden="true">
        <defs>
          <mask id="mask-near">
            <rect x="0" y="0" width="1920" height="1080" fill="white"/>
            <circle cx="120" cy="120" r="56" fill="black"/>
            <circle cx="420" cy="220" r="36" fill="black"/>
            <circle cx="740" cy="160" r="52" fill="black"/>
            <circle cx="1140" cy="240" r="58" fill="black"/>
            <circle cx="1740" cy="220" r="44" fill="black"/>
            <circle cx="220" cy="520" r="62" fill="black"/>
            <circle cx="600" cy="660" r="40" fill="black"/>
            <circle cx="980" cy="600" r="54" fill="black"/>
            <circle cx="1340" cy="660" r="62" fill="black"/>
            <circle cx="320" cy="920" r="60" fill="black"/>
            <circle cx="880" cy="920" r="46" fill="black"/>
            <circle cx="1380" cy="900" r="56" fill="black"/>
          </mask>
        </defs>
        <rect x="0" y="0" width="1920" height="1080"
              fill="rgba(77,163,255,0.18)" mask="url(#mask-near)"/>
      </svg>

      <div class="floor-shadow"></div>

      <!-- GRID (front content) -->
      <section class="grid" id="grid" aria-label="Portfolio items">
        <!-- 12 cards -->
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Landing Page Redesign</div><div class="tag">Web</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">E-commerce UI Kit</div><div class="tag">UI</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Motion Graphics Reel</div><div class="tag">Motion</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Brand System “Aurora”</div><div class="tag">Branding</div></div></article>

        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Mobile App Dashboard</div><div class="tag">App</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">3D Product Shots</div><div class="tag">3D</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Data Viz Suite</div><div class="tag">Analytics</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Illustration Pack</div><div class="tag">Art</div></div></article>

        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Marketing Microsite</div><div class="tag">Web</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Icon Set “Orbit”</div><div class="tag">Icons</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Corporate Templates</div><div class="tag">Docs</div></div></article>
        <article class="card" tabindex="0"><div class="thumb"></div><div class="veil"></div><div class="meta"><div class="title">Photography Series</div><div class="tag">Photo</div></div></article>
      </section>
    </div>
  </div>

<script>
(() => {
  const scene = document.getElementById('scene');
  const stage = document.getElementById('stage');
  const cards = Array.from(document.querySelectorAll('.card'));

  // Camera config
  const maxRotateX = 10;   // deg up/down
  const maxRotateY = 14;   // deg left/right
  const ease = 0.12;       // camera easing
  const maxCardDepth = 30; // px forward based on pointer proximity

  let targetRX = 0, targetRY = 0;
  let curRX = 0, curRY = 0;

  // Cache card rects
  const measure = () => cards.map(el => {
    const r = el.getBoundingClientRect();
    return { el, cx: r.left + r.width/2, cy: r.top + r.height/2, w: r.width, h: r.height };
  });
  let cached = measure();
  window.addEventListener('resize', () => { cached = measure(); });

  // Normalize pointer to [-1,1]
  const norm = (x, y) => {
    const r = scene.getBoundingClientRect();
    return {
      nx: Math.max(-1, Math.min(1, (x - (r.left + r.width/2)) / (r.width/2))),
      ny: Math.max(-1, Math.min(1, (y - (r.top + r.height/2)) / (r.height/2)))
    };
  };

  function onMove(e){
    const p = e.touches ? e.touches[0] : e;
    const { nx, ny } = norm(p.clientX, p.clientY);

    // Update CSS vars for parallax layers
    stage.style.setProperty('--mx', nx.toFixed(4));
    stage.style.setProperty('--my', ny.toFixed(4));

    // Camera target rotations (center => 0/0)
    targetRY = -nx * maxRotateY;
    targetRX =  ny * maxRotateX;

    // Depth per card relative to pointer proximity
    cached.forEach(({ el, cx, cy, w, h }) => {
      const dx = (p.clientX - cx) / (w * 0.5);
      const dy = (p.clientY - cy) / (h * 0.5);
      const d = Math.hypot(dx, dy);
      const influence = Math.max(0, 1 - d);  // 1 at center of card
      el.style.setProperty('--dz', `${(influence * maxCardDepth).toFixed(1)}px`);
    });
  }

  function reset(){
    targetRX = 0; targetRY = 0;
    stage.style.setProperty('--mx', '0');
    stage.style.setProperty('--my', '0');
    cards.forEach(el => el.style.setProperty('--dz', `0px`));
  }

  window.addEventListener('pointermove', onMove, { passive:true });
  window.addEventListener('touchmove', onMove, { passive:true });
  window.addEventListener('pointerleave', reset);
  window.addEventListener('blur', reset);

  // RAF camera easing
  (function tick(){
    curRX += (targetRX - curRX) * ease;
    curRY += (targetRY - curRY) * ease;
    stage.style.setProperty('--rx', `${curRX.toFixed(3)}deg`);
    stage.style.setProperty('--ry', `${curRY.toFixed(3)}deg`);
    requestAnimationFrame(tick);
  })();

  // Accessibility: focus bump
  cards.forEach(c => {
    c.addEventListener('focus', () => c.style.setProperty('--dz', `40px`));
    c.addEventListener('blur',  () => c.style.setProperty('--dz', `0px`));
  });
})();
</script>
</body>
</html>
```
## 7.Isometrico
**Isometrico.html**
```html
<!doctype html>
<html>
  <head>
  </head>
  <body>
    <canvas id="lienzo"></canvas>
    <script>
      class Recogible{
        constructor(){
          this.x = Math.round(Math.random()*1000);
          this.y = Math.round(Math.random()*1000);
        }
        dibuja(){
          let puntoiso = iso(this.x,this.y)
          contexto.beginPath();
          contexto.fillStyle = "green"
          contexto.arc(this.x,this.y,8,0,Math.PI*2)
          contexto.fill()
        }
      }
      class Personaje{
        constructor(){
          this.x = 10;
          this.y = 10;
          this.d = 0;
          this.andando = false;
        }
        dibuja(){
          let puntoiso = iso(this.x,this.y)
          contexto.drawImage(sprite, this.d*64, 0, 64, 77, puntoiso.x,puntoiso.y, 64, 77) 
        }
      }
    
      var lienzo = document.querySelector("#lienzo")
      var contexto = lienzo.getContext("2d")
      lienzo.width = 1024
      lienzo.height = 1024
      var Personaje1 = new Personaje();
      var sprite = new Image()
      sprite.src = "spritesheet.png"
      var recogibles = []
      var numerorecogibles = 50;
      for(let i = 0;i<numerorecogibles;i++){
        recogibles.push(new Recogible())
      }
      
      // Ahora dibujo una rejilla isométrica "fake" (2:1, alineada con el suelo)
      
      var paso = 20

      // Función de proyección isométrica simple (2:1)
      function iso(i, j){
        return {
          x: 512 + (i - j) * paso,
          y: 512 + (i + j) * (paso / 2)
        }
      }
      function dibujoRejilla(){
        // Limpio fondo
        contexto.fillStyle = "#fff"
        contexto.fillRect(0,0,1024,1024)
        contexto.strokeStyle = "#000"

        // Líneas paralelas al eje U (j variable, i constante)
        for (let i = -60; i <= 60; i++) {
          const a = iso(i, -60);
          const b = iso(i,  60);
          contexto.beginPath();
          contexto.moveTo(a.x, a.y);
          contexto.lineTo(b.x, b.y);
          contexto.stroke();
        }

        // Líneas paralelas al eje V (i variable, j constante)
        for (let j = -60; j <= 60; j++) {
          const a = iso(-60, j);
          const b = iso( 60, j);
          contexto.beginPath();
          contexto.moveTo(a.x, a.y);
          contexto.lineTo(b.x, b.y);
          contexto.stroke();
        }
      }

      document.onkeydown = function(event){
        switch(event.key){
          case "w":
                          Personaje1.d = 3
            Personaje1.andando = true;
            break;
          case "s":
                        Personaje1.d = 1 // correcto  
            Personaje1.andando = true;
            break;
          case "a":
                        Personaje1.d = 0
            Personaje1.andando = true;
            break;
          case "d":
                        Personaje1.d = 2
            Personaje1.andando = true;
            break;
        }
        }
        document.onkeyup = function(event){
        switch(event.key){
          case "w":
            Personaje1.andando = false;
            break;
          case "s":
            Personaje1.andando = false;
            break;
          case "a":
            Personaje1.andando = false;
            break;
          case "d":
            Personaje1.andando = false;
            break;
        }  
      
      }
      
      let temporizador = setTimeout("bucle()",1000)
      
      function bucle(){
        if(Personaje1.andando == true){
        switch(Personaje1.d){
            case 3:
              Personaje1.x--
              break;
            case 1:
              Personaje1.x++
              break;
            case 0:
              Personaje1.y++
              break;
            case 2:
              Personaje1.y--
              break;
          }
        }
        dibujoRejilla()
        for(let i = 0;i<numerorecogibles;i++){
          recogibles[i].dibuja()
        }
        Personaje1.dibuja()
        clearTimeout(temporizador)
        temporizador = setTimeout("bucle()",66)
      }
    </script>
  </body>
</html>
```
## 8.Minecraft
**8.Minecraft.html**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Minecraft Clone</title>
    <style>
        body { margin: 0; overflow: hidden; font-family: Arial; }
        canvas { display: block; }
        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 20px;
            height: 20px;
            transform: translate(-50%, -50%);
            pointer-events: none;
        }
        #crosshair::before, #crosshair::after {
            content: '';
            position: absolute;
            background: white;
        }
        #crosshair::before {
            width: 2px;
            height: 20px;
            left: 50%;
            transform: translateX(-50%);
        }
        #crosshair::after {
            width: 20px;
            height: 2px;
            top: 50%;
            transform: translateY(-50%);
        }
        #ui {
            position: absolute;
            top: 10px;
            left: 10px;
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 10px;
            border-radius: 5px;
            pointer-events: none;
        }
        #block-selector {
            position: absolute;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0,0,0,0.7);
            padding: 10px;
            border-radius: 5px;
            display: flex;
            gap: 10px;
        }
        .block-option {
            width: 40px;
            height: 40px;
            border: 2px solid transparent;
            border-radius: 3px;
            cursor: pointer;
            pointer-events: auto;
        }
        .block-option.selected {
            border-color: white;
        }
        #reset-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            background: rgba(255,0,0,0.7);
            color: white;
            border: none;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            pointer-events: auto;
        }
        #reset-btn:hover {
            background: rgba(255,0,0,0.9);
        }
    </style>
</head>
<body>
    <div id="crosshair"></div>
    <div id="ui">
        <div>WASD: Move | Mouse: Look</div>
        <div>Space: Jump | LClick: Remove | RClick: Place</div>
        <div>1,2,3: Select Block Type | World auto-saves</div>
    </div>
    <div id="block-selector">
        <div class="block-option selected" data-type="grass" style="background: #4CAF50;"></div>
        <div class="block-option" data-type="dirt" style="background: #795548;"></div>
        <div class="block-option" data-type="stone" style="background: #9E9E9E;"></div>
    </div>
    <button id="reset-btn">Reset World</button>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/PointerLockControls.min.js"></script>
    
    <!-- Postprocessing Dependencies -->
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/CopyShader.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/SSAOShader.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/SAOShader.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/EffectComposer.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/RenderPass.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/ShaderPass.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/SSAOPass.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/math/SimplexNoise.js"></script>

    <script>
        // Block class
        class Block {
            constructor(x, y, z, type = 'grass') {
                this.x = x;
                this.y = y;
                this.z = z;
                this.type = type;
                this.mesh = null;
                
                this.createMesh();
            }
            
            createMesh() {
                const geometry = new THREE.BoxGeometry(1, 1, 1);
                
                const colors = {
                    'grass': 0x4CAF50,
                    'dirt': 0x795548,
                    'stone': 0x9E9E9E
                };

                // Create canvas for texture
                const canvas = document.createElement('canvas');
                canvas.width = 64;
                canvas.height = 64;
                const context = canvas.getContext('2d');

                // Base color
                const color = colors[this.type] || 0x4CAF50;
                const r = (color >> 16) & 255;
                const g = (color >> 8) & 255;
                const b = color & 255;

                // Fill with base color
                context.fillStyle = `rgb(${r}, ${g}, ${b})`;
                context.fillRect(0, 0, 64, 64);

                // Add texture pattern (simulated noise/pattern)
                context.globalAlpha = 0.3;
                for (let i = 0; i < 200; i++) {
                    const x = Math.random() * 64;
                    const y = Math.random() * 64;
                    const size = Math.random() * 3 + 1;
                    const brightness = Math.random() * 50 - 25;
                    
                    context.fillStyle = `rgb(${Math.max(0, Math.min(255, r + brightness))}, 
                                            ${Math.max(0, Math.min(255, g + brightness))}, 
                                            ${Math.max(0, Math.min(255, b + brightness))})`;
                    context.fillRect(x, y, size, size);
                }

                // Create texture from canvas
                const texture = new THREE.CanvasTexture(canvas);
                texture.wrapS = THREE.RepeatWrapping;
                texture.wrapT = THREE.RepeatWrapping;
                texture.repeat.set(1, 1);
                
                texture.magFilter = THREE.NearestFilter;
                texture.minFilter = THREE.NearestFilter;
                texture.generateMipmaps = false;

                const material = new THREE.MeshPhongMaterial({ 
                    map: texture,
                    color: new THREE.Color(color)
                });
                
                this.mesh = new THREE.Mesh(geometry, material);
                this.mesh.position.set(this.x, this.y, this.z);
                
                // Enable shadows
                this.mesh.castShadow = true;
                this.mesh.receiveShadow = true;
            }
            
            addToScene(scene) {
                if (this.mesh) {
                    scene.add(this.mesh);
                }
            }
            
            removeFromScene(scene) {
                if (this.mesh) {
                    scene.remove(this.mesh);
                }
            }
            
            getBoundingBox() {
                return new THREE.Box3().setFromObject(this.mesh);
            }

            serialize() {
                return {
                    x: this.x,
                    y: this.y,
                    z: this.z,
                    type: this.type
                };
            }
        }

        // Game class
        class MinecraftGame {
            constructor() {
                this.scene = new THREE.Scene();
                this.scene.background = new THREE.Color(0x87CEEB);
                
                this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
                this.camera.position.set(0, 2, 5);
                
                this.renderer = new THREE.WebGLRenderer({ antialias: true });
                this.renderer.setSize(window.innerWidth, window.innerHeight);
                this.renderer.shadowMap.enabled = true;
                this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;
                document.body.appendChild(this.renderer.domElement);
                
                // Postprocessing setup
                this.setupPostProcessing();
                
                this.controls = null;
                this.blocks = [];
                this.moveState = { forward: false, backward: false, left: false, right: false, jump: false };
                this.velocity = new THREE.Vector3();
                
                // Block selection
                this.selectedBlockType = 'grass';
                
                // Physics properties
                this.onGround = false;
                this.gravity = -20;
                this.jumpForce = 8;
                this.playerHeight = 1.8;
                this.playerRadius = 0.3;
                
                // Raycasting
                this.raycaster = new THREE.Raycaster();
                this.raycaster.far = 10;
                
                this.setupLights();
                this.setupControls();
                this.setupEventListeners();
                this.setupUI();
                
                // Load saved world or create default
                this.loadWorld();
                
                this.clock = new THREE.Clock();
                this.animate();
            }
            
            setupPostProcessing() {
                // Create effect composer
                this.composer = new THREE.EffectComposer(this.renderer);
                
                // Add render pass
                const renderPass = new THREE.RenderPass(this.scene, this.camera);
                this.composer.addPass(renderPass);
                
                // Add SSAO pass
                this.ssaoPass = new THREE.SSAOPass(this.scene, this.camera, window.innerWidth, window.innerHeight);
                this.ssaoPass.kernelRadius = 16;
                this.ssaoPass.minDistance = 0.005;
                this.ssaoPass.maxDistance = 0.1;
                this.ssaoPass.output = THREE.SSAOPass.OUTPUT.Default;
                
                // Adjust SSAO intensity for better visibility
                this.ssaoPass.kernelSize = 32;
                this.ssaoPass.kernelRadius = 32;
                
                this.composer.addPass(this.ssaoPass);
            }
            
            setupLights() {
                const ambientLight = new THREE.AmbientLight(0x404040, 2);
                this.scene.add(ambientLight);
                
                const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
                directionalLight.position.set(50, 50, 50);
                directionalLight.castShadow = true;
                
                // Shadow properties
                directionalLight.shadow.mapSize.width = 2048;
                directionalLight.shadow.mapSize.height = 2048;
                directionalLight.shadow.camera.near = 0.5;
                directionalLight.shadow.camera.far = 500;
                directionalLight.shadow.camera.left = -50;
                directionalLight.shadow.camera.right = 50;
                directionalLight.shadow.camera.top = 50;
                directionalLight.shadow.camera.bottom = -50;
                
                this.scene.add(directionalLight);
            }
            
            createDefaultWorld() {
                // Clear existing blocks
                this.blocks.forEach(block => block.removeFromScene(this.scene));
                this.blocks = [];
                
                const gridSize = 10;
                
                // Create floor
                for (let x = -gridSize/2; x < gridSize/2; x++) {
                    for (let z = -gridSize/2; z < gridSize/2; z++) {
                        let type = 'grass';
                        if (Math.random() > 0.8) type = 'dirt';
                        if (Math.random() > 0.95) type = 'stone';
                        
                        const block = new Block(x, -0.5, z, type);
                        block.addToScene(this.scene);
                        this.blocks.push(block);
                    }
                }
                
                // Add some raised blocks
                for (let i = 0; i < 5; i++) {
                    const x = Math.floor(Math.random() * 8 - 4);
                    const z = Math.floor(Math.random() * 8 - 4);
                    const block = new Block(x, 0.5, z, 'stone');
                    block.addToScene(this.scene);
                    this.blocks.push(block);
                }
                
                this.saveWorld();
            }
            
            setupControls() {
                this.controls = new THREE.PointerLockControls(this.camera, document.body);
                
                const instructions = document.createElement('div');
                instructions.style.cssText = `
                    position: absolute;
                    top: 50%;
                    left: 50%;
                    transform: translate(-50%, -50%);
                    background: rgba(0,0,0,0.8);
                    color: white;
                    padding: 20px;
                    border-radius: 10px;
                    text-align: center;
                    font-family: Arial;
                `;
                instructions.innerHTML = `
                    <h2>Minecraft Clone</h2>
                    <p>Click to play</p>
                    <p>WASD: Move | Mouse: Look around</p>
                    <p>Space: Jump | Left Click: Remove | Right Click: Place</p>
                    <p>1,2,3: Select Block Type | World auto-saves</p>
                `;
                document.body.appendChild(instructions);
                
                const startGame = () => {
                    this.controls.lock();
                    instructions.style.display = 'none';
                };
                
                document.body.addEventListener('click', startGame);
                
                this.controls.addEventListener('lock', () => {
                    instructions.style.display = 'none';
                });
                
                this.controls.addEventListener('unlock', () => {
                    instructions.style.display = 'block';
                });
            }
            
            setupUI() {
                // Block selector
                const blockOptions = document.querySelectorAll('.block-option');
                blockOptions.forEach(option => {
                    option.addEventListener('click', () => {
                        blockOptions.forEach(opt => opt.classList.remove('selected'));
                        option.classList.add('selected');
                        this.selectedBlockType = option.dataset.type;
                    });
                });

                // Reset button
                document.getElementById('reset-btn').addEventListener('click', () => {
                    if (confirm('Are you sure you want to reset the world?')) {
                        this.createDefaultWorld();
                    }
                });
            }
            
            setupEventListeners() {
                document.addEventListener('keydown', (event) => {
                    switch (event.code) {
                        case 'KeyW':
                            this.moveState.forward = true;
                            break;
                        case 'KeyS':
                            this.moveState.backward = true;
                            break;
                        case 'KeyA':
                            this.moveState.left = true;
                            break;
                        case 'KeyD':
                            this.moveState.right = true;
                            break;
                        case 'Space':
                            if (this.onGround) {
                                this.moveState.jump = true;
                            }
                            event.preventDefault();
                            break;
                        case 'Digit1':
                            this.selectBlockType('grass');
                            break;
                        case 'Digit2':
                            this.selectBlockType('dirt');
                            break;
                        case 'Digit3':
                            this.selectBlockType('stone');
                            break;
                    }
                });
                
                document.addEventListener('keyup', (event) => {
                    switch (event.code) {
                        case 'KeyW':
                            this.moveState.forward = false;
                            break;
                        case 'KeyS':
                            this.moveState.backward = false;
                            break;
                        case 'KeyA':
                            this.moveState.left = false;
                            break;
                        case 'KeyD':
                            this.moveState.right = false;
                            break;
                        case 'Space':
                            this.moveState.jump = false;
                            break;
                    }
                });

                // Mouse click events
                document.addEventListener('mousedown', (event) => {
                    if (!this.controls.isLocked) return;
                    
                    if (event.button === 0) { // Left click
                        this.removeBlockAtPointer();
                    } else if (event.button === 2) { // Right click
                        this.placeBlockAtPointer();
                        event.preventDefault();
                    }
                });

                document.addEventListener('contextmenu', (event) => {
                    event.preventDefault();
                });
                
                window.addEventListener('resize', () => {
                    this.camera.aspect = window.innerWidth / window.innerHeight;
                    this.camera.updateProjectionMatrix();
                    this.renderer.setSize(window.innerWidth, window.innerHeight);
                    
                    // Update postprocessing
                    this.composer.setSize(window.innerWidth, window.innerHeight);
                });

                // Auto-save when leaving page
                window.addEventListener('beforeunload', () => {
                    this.saveWorld();
                });
            }

            selectBlockType(type) {
                this.selectedBlockType = type;
                const blockOptions = document.querySelectorAll('.block-option');
                blockOptions.forEach(option => {
                    option.classList.toggle('selected', option.dataset.type === type);
                });
            }

            saveWorld() {
                const worldData = {
                    blocks: this.blocks.map(block => block.serialize()),
                    playerPosition: {
                        x: this.camera.position.x,
                        y: this.camera.position.y,
                        z: this.camera.position.z
                    }
                };
                localStorage.setItem('minecraftWorld', JSON.stringify(worldData));
                console.log('World saved!');
            }

            loadWorld() {
                const saved = localStorage.getItem('minecraftWorld');
                if (saved) {
                    try {
                        const worldData = JSON.parse(saved);
                        
                        // Clear existing blocks
                        this.blocks.forEach(block => block.removeFromScene(this.scene));
                        this.blocks = [];
                        
                        // Load blocks
                        worldData.blocks.forEach(blockData => {
                            const block = new Block(blockData.x, blockData.y, blockData.z, blockData.type);
                            block.addToScene(this.scene);
                            this.blocks.push(block);
                        });
                        
                        // Load player position
                        if (worldData.playerPosition) {
                            this.camera.position.set(
                                worldData.playerPosition.x,
                                worldData.playerPosition.y,
                                worldData.playerPosition.z
                            );
                        }
                        
                        console.log('World loaded!');
                    } catch (e) {
                        console.error('Error loading world:', e);
                        this.createDefaultWorld();
                    }
                } else {
                    this.createDefaultWorld();
                }
            }

            removeBlockAtPointer() {
                this.raycaster.setFromCamera(new THREE.Vector2(0, 0), this.camera);
                const blockMeshes = this.blocks.map(block => block.mesh);
                const intersects = this.raycaster.intersectObjects(blockMeshes);
                
                if (intersects.length > 0) {
                    const intersection = intersects[0];
                    const blockMesh = intersection.object;
                    
                    const blockIndex = this.blocks.findIndex(block => block.mesh === blockMesh);
                    if (blockIndex !== -1) {
                        const block = this.blocks[blockIndex];
                        block.removeFromScene(this.scene);
                        this.blocks.splice(blockIndex, 1);
                        this.saveWorld();
                        console.log('Block removed!');
                    }
                }
            }

            placeBlockAtPointer() {
                this.raycaster.setFromCamera(new THREE.Vector2(0, 0), this.camera);
                const blockMeshes = this.blocks.map(block => block.mesh);
                const intersects = this.raycaster.intersectObjects(blockMeshes);
                
                if (intersects.length > 0) {
                    const intersection = intersects[0];
                    const face = intersection.face;
                    const blockMesh = intersection.object;
                    
                    const normal = intersection.face.normal.clone();
                    normal.transformDirection(blockMesh.matrixWorld);
                    
                    const hitBlock = this.blocks.find(block => block.mesh === blockMesh);
                    if (!hitBlock) return;
                    
                    const newPosition = new THREE.Vector3(
                        hitBlock.x + Math.round(normal.x),
                        hitBlock.y + Math.round(normal.y),
                        hitBlock.z + Math.round(normal.z)
                    );
                    
                    const positionOccupied = this.blocks.some(block => 
                        block.x === newPosition.x && 
                        block.y === newPosition.y && 
                        block.z === newPosition.z
                    );
                    
                    const playerPosition = this.camera.position.clone();
                    const distanceToPlayer = newPosition.distanceTo(playerPosition);
                    
                    if (!positionOccupied && distanceToPlayer > 1.5) {
                        const newBlock = new Block(newPosition.x, newPosition.y, newPosition.z, this.selectedBlockType);
                        newBlock.addToScene(this.scene);
                        this.blocks.push(newBlock);
                        this.saveWorld();
                        console.log(`Block placed at (${newPosition.x}, ${newPosition.y}, ${newPosition.z})`);
                    }
                }
            }
            
            checkCollision(newPosition) {
                const playerBox = new THREE.Box3(
                    new THREE.Vector3(
                        newPosition.x - this.playerRadius,
                        newPosition.y - this.playerHeight,
                        newPosition.z - this.playerRadius
                    ),
                    new THREE.Vector3(
                        newPosition.x + this.playerRadius,
                        newPosition.y,
                        newPosition.z + this.playerRadius
                    )
                );
                
                for (const block of this.blocks) {
                    const blockBox = block.getBoundingBox();
                    if (playerBox.intersectsBox(blockBox)) {
                        return true;
                    }
                }
                return false;
            }
            
            updateMovement(delta) {
                if (!this.controls.isLocked) return;
                
                const speed = 5.0;
                
                const cameraDirection = new THREE.Vector3();
                this.camera.getWorldDirection(cameraDirection);
                cameraDirection.y = 0;
                cameraDirection.normalize();
                
                const cameraRight = new THREE.Vector3();
                cameraRight.crossVectors(this.camera.up, cameraDirection).normalize();
                
                this.velocity.x = 0;
                this.velocity.z = 0;
                
                if (this.moveState.forward) {
                    this.velocity.add(cameraDirection.clone().multiplyScalar(speed * delta));
                }
                if (this.moveState.backward) {
                    this.velocity.sub(cameraDirection.clone().multiplyScalar(speed * delta));
                }
                if (this.moveState.left) {
                    this.velocity.add(cameraRight.clone().multiplyScalar(speed * delta));
                }
                if (this.moveState.right) {
                    this.velocity.sub(cameraRight.clone().multiplyScalar(speed * delta));
                }
                
                const oldPosition = this.camera.position.clone();
                const newHorizontalPos = oldPosition.clone().add(this.velocity);
                if (!this.checkCollision(newHorizontalPos)) {
                    this.camera.position.x = newHorizontalPos.x;
                    this.camera.position.z = newHorizontalPos.z;
                }
                
                if (this.moveState.jump && this.onGround) {
                    this.velocity.y = this.jumpForce;
                    this.onGround = false;
                    this.moveState.jump = false;
                }
                
                this.velocity.y += this.gravity * delta;
                
                const newVerticalPos = this.camera.position.clone();
                newVerticalPos.y += this.velocity.y * delta;
                
                const gridSize = 10;
                const isOutsideGrid = Math.abs(this.camera.position.x) > gridSize/2 || 
                                    Math.abs(this.camera.position.z) > gridSize/2;
                
                if (isOutsideGrid) {
                    if (!this.checkCollision(newVerticalPos)) {
                        this.camera.position.y = newVerticalPos.y;
                    }
                    this.onGround = false;
                } else {
                    if (!this.checkCollision(newVerticalPos)) {
                        this.camera.position.y = newVerticalPos.y;
                        this.onGround = false;
                    } else {
                        if (this.velocity.y < 0) {
                            this.onGround = true;
                            this.velocity.y = 0;
                        } else {
                            this.velocity.y = 0;
                        }
                    }
                }
                
                if (this.camera.position.y < -10) {
                    this.camera.position.set(0, 5, 0);
                    this.velocity.set(0, 0, 0);
                }

                // Auto-save every 10 seconds
                if (Math.floor(this.clock.getElapsedTime()) % 10 === 0) {
                    this.saveWorld();
                }
            }
            
            animate() {
                requestAnimationFrame(() => this.animate());
                
                const delta = Math.min(this.clock.getDelta(), 0.1);
                
                if (this.controls.isLocked) {
                    this.updateMovement(delta);
                }
                
                // Use composer instead of renderer
                this.composer.render();
            }
        }

        // Start the game
        new MinecraftGame();
    </script>
</body>
</html>
```
## Informe