# Ecuaciones
Ecuaciones desde básicas a complejas
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reto de Ecuaciones Prof. Juan</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
    <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/contrib/auto-render.min.js" onload="renderMathInElement(document.body);"></script>

    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background-color: #f0f2f5; color: #333; display: flex; justify-content: center; align-items: center; min-height: 100vh; margin: 0; padding: 20px; box-sizing: border-box; }
        .container { background-color: #fff; padding: 30px; border-radius: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 100%; max-width: 500px; text-align: center; }
        h1 { color: #2c3e50; font-size: 1.5rem; margin-bottom: 10px; }
        #nivel-display { color: #7f8c8d; font-weight: bold; margin-bottom: 20px; }
        #ecuacion-container { font-size: 2rem; margin: 25px 0; color: #1a1a1a; min-height: 2.5rem;}
        input[type="number"] { width: 80%; padding: 12px; font-size: 1.2rem; border: 2px solid #ddd; border-radius: 10px; margin-bottom: 15px; -moz-appearance: textfield; }
        input[type="number"]::-webkit-outer-spin-button, input[type="number"]::-webkit-inner-spin-button { -webkit-appearance: none; margin: 0; }
        .btn-principal { background-color: #3498db; color: white; border: none; padding: 12px 24px; font-size: 1.1rem; border-radius: 10px; cursor: pointer; transition: background 0.3s; width: 100%; margin-bottom: 10px;}
        .btn-principal:hover { background-color: #2980b9; }
        .btn-secundario { background-color: #ecf0f1; color: #7f8c8d; border: none; padding: 8px 16px; font-size: 0.9rem; border-radius: 8px; cursor: pointer; display: none;}
        #feedback-container { margin-top: 20px; padding: 15px; border-radius: 10px; display: none; text-align: left; }
        .correcto { background-color: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .incorrecto { background-color: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
        #explicacion-pasos { margin-top: 10px; font-size: 0.95rem; line-height: 1.5; border-top: 1px solid rgba(0,0,0,0.1); padding-top: 10px; }
    </style>
</head>
<body>

<div class="container">
    <h1>Reto de Ecuaciones</h1>
    <div id="nivel-display">Nivel: 1 (Básico)</div>
    
    <div id="ecuacion-container">
        <span id="ecuacion-math"></span>
    </div>

    <input type="number" id="respuesta-usuario" placeholder="Tu respuesta para x" pattern="[0-9]*" inputmode="numeric">
    
    <button class="btn-principal" onclick="comprobarRespuesta()">Comprobar</button>
    <button class="btn-principal" id="btn-siguiente" style="display: none; background-color: #2ecc71;" onclick="siguienteEjercicio()">¡Siguiente!</button>
    
    <div id="feedback-container">
        <div id="feedback-titulo" style="font-weight: bold;"></div>
        <div id="feedback-mensaje"></div>
        <button class="btn-secundario" id="btn-explicacion" onclick="toggleExplicacion()">Ver explicación paso a paso</button>
        <div id="explicacion-pasos" style="display: none;"></div>
    </div>
</div>

<script>
    // Variables de estado del juego
    let nivelActual = 1;
    let aciertosSeguidos = 0;
    let datosEjercicio = {};
    const aciertosParaSubir = 3; // Cuántos aciertos seguidos para subir de nivel

    // Elementos del DOM
    const htmlEcuacion = document.getElementById('ecuacion-math');
    const inputRespuesta = document.getElementById('respuesta-usuario');
    const htmlFeedback = document.getElementById('feedback-container');
    const htmlFeedbackTitulo = document.getElementById('feedback-titulo');
    const htmlFeedbackMensaje = document.getElementById('feedback-mensaje');
    const htmlExplicacion = document.getElementById('explicacion-pasos');
    const btnExplicacion = document.getElementById('btn-explicacion');
    const btnComprobar = document.querySelector('.btn-principal');
    const btnSiguiente = document.getElementById('btn-siguiente');
    const htmlNivel = document.getElementById('nivel-display');

    // Función para renderizar mates con KaTeX
    function renderMates(elemento, textoLaTeX) {
        katex.render(textoLaTeX, elemento, { throwOnError: false });
    }

    // Generador de números aleatorios (evitando el cero si es necesario)
    function randomInt(min, max, evitarCero = false) {
        let num = Math.floor(Math.random() * (max - min + 1)) + min;
        if (evitarCero && num === 0) return randomInt(min, max, evitarCero);
        return num;
    }

    // --- GENERACIÓN DE EJERCICIOS POR NIVEL ---
    function generarEjercicio() {
        let a, b, c, x;
        let explicacion = "";
        let ecuacionLaTeX = "";

        // Reseteamos la interfaz
        inputRespuesta.value = '';
        inputRespuesta.style.display = 'inline-block';
        btnComprobar.style.display = 'inline-block';
        btnSiguiente.style.display = 'none';
        htmlFeedback.style.display = 'none';
        htmlExplicacion.style.display = 'none';
        btnExplicacion.style.display = 'none';
        
        inputRespuesta.focus(); // Enfocar el input para el alumno

        switch(nivelActual) {
            case 1: // Básico: x + b = c  (a es siempre 1)
                x = randomInt(-10, 10);
                b = randomInt(-15, 15, true); // Evitamos sumar 0
                c = x + b;
                
                // Formateo del signo b para LaTeX: x + (-5) -> x - 5
                let bStrN1 = b > 0 ? `+ ${b}` : `- ${Math.abs(b)}`;
                ecuacionLaTeX = `x ${bStrN1} = ${c}`;
                
                explicacion = `<b>Objetivo:</b> Dejar la $x$ sola.<br>
                               1. Tenemos la ecuación: $${ecuacionLaTeX}$.<br>
                               2. El $${b}$ está sumando (o restando) a la $x$. Para quitarlo, lo pasamos al otro lado del igual haciendo lo contrario.<br>
                               3. Si estaba como $${bStrN1}$, pasa al otro lado como $${b > 0 ? '-' : '+'} ${Math.abs(b)}$.<br>
                               4. $x = ${c} ${b > 0 ? '-' : '+'} ${Math.abs(b)}$.<br>
                               5. Hacemos la operación: $x = ${x}$.`;
                break;

            case 2: // Medio: a*x = c (Sumas/restas son 0)
                a = randomInt(-5, 5, true); // a distinto de 0
                x = randomInt(-10, 10);
                c = a * x;
                
                ecuacionLaTeX = `${a}x = ${c}`;
                
                explicacion = `<b>Objetivo:</b> Dejar la $x$ sola.<br>
                               1. Tenemos la ecuación: $${ecuacionLaTeX}$.<br>
                               2. El $${a}$ está **multiplicando** a la $x$ (aunque no veas el punto, está ahí: $${a} \\cdot x$).<br>
                               3. Para quitar el $${a}$, lo pasamos al otro lado haciendo lo contrario: **dividiendo**.<br>
                               4. $x = \\frac{${c}}{${a}}$.<br>
                               5. Hacemos la división: $x = ${x}$.`;
                break;

            case 3: // Avanzado: a*x + b = c
            default:
                a = randomInt(-5, 5, true); 
                x = randomInt(-10, 10);
                b = randomInt(-20, 20, true);
                c = (a * x) + b;

                let bStrN3 = b > 0 ? `+ ${b}` : `- ${Math.abs(b)}`;
                ecuacionLaTeX = `${a}x ${bStrN3} = ${c}`;
                
                let c_menos_b = c - b;

                explicacion = `<b>Objetivo:</b> Dejar la $x$ sola. Sigue el orden inverso a la jerarquía de operaciones (primero sumas/restas, luego multiplicaciones/divisiones).<br>
                               1. Ecuación: $${ecuacionLaTeX}$.<br>
                               2. **Paso 1:** Quitamos lo que suma o resta. El $${bStrN3}$ pasa al otro lado cambiando de signo.<br>
                               3. $${a}x = ${c} ${b > 0 ? '-' : '+'} ${Math.abs(b)}$.<br>
                               4. Calculamos ese lado: $${a}x = ${c_menos_b}$.<br>
                               5. **Paso 2:** Quitamos el $${a}$ que multiplica a la $x$. Pasa dividiendo al otro lado.<br>
                               6. $x = \\frac{${c_menos_b}}{${a}}$.<br>
                               7. Resultado: $x = ${x}$.`;
                nivelActual = 3; // Asegurar que no suba más
                break;
        }

        datosEjercicio = { a:a, b:b, c:c, xSol:x, explicacion: explicacion };
        renderMates(htmlEcuacion, ecuacionLaTeX);
        htmlNivel.innerText = `Nivel: ${nivelActual} (${nivelActual === 1 ? 'Fácil' : nivelActual === 2 ? 'Medio' : 'Avanzado'})`;
    }

    // --- LÓGICA DE COMPROBACIÓN ---
    function comprobarRespuesta() {
        const respuestaNum = parseInt(inputRespuesta.value);
        
        if (isNaN(respuestaNum)) {
            alert("Por favor, escribe un número antes de comprobar.");
            return;
        }

        htmlFeedback.style.display = 'block';
        htmlExplicacion.style.display = 'none'; // Asegurar que empieza oculta
        htmlExplicacion.innerHTML = datosEjercicio.explicacion; 
        renderMathInElement(htmlExplicacion); // Renderizar las mates dentro de la explicación

        if (respuestaNum === datosEjercicio.xSol) {
            // RESPUESTA CORRECTA
            aciertosSeguidos++;
            htmlFeedback.className = 'correcto';
            htmlFeedbackTitulo.innerText = "¡Muy bien!";
            htmlFeedbackMensaje.innerText = "¡Has acertado!";
            btnExplicacion.style.display = 'none';
            btnComprobar.style.display = 'none';
            btnSiguiente.style.display = 'inline-block';
            inputRespuesta.style.display = 'none';

            // Comprobamos si subimos de nivel
            if (nivelActual < 3 && aciertosSeguidos >= aciertosParaSubir) {
                nivelActual++;
                aciertosSeguidos = 0; // Reseteamos aciertos al subir
                alert(`¡Genial! Has acertado ${aciertosParaSubir} seguidas. ¡Subimos al Nivel ${nivelActual}!`);
            }

        } else {
            // RESPUESTA INCORRECTA
            aciertosSeguidos = 0; // Resetear racha si falla
            htmlFeedback.className = 'incorrecto';
            htmlFeedbackTitulo.innerText = "Casi...";
            htmlFeedbackMensaje.innerText = `La respuesta no es ${respuestaNum}. Prueba de nuevo o revisa cómo se hace.`;
            btnExplicacion.style.display = 'inline-block';
            btnComprobar.innerText = "Reintentar";
        }
    }

    // --- UTILIDADES DE INTERFAZ ---
    function siguienteEjercicio() {
        generarEjercicio();
        btnComprobar.innerText = "Comprobar";
    }

    function toggleExplicacion() {
        if (htmlExplicacion.style.display === 'none') {
            htmlExplicacion.style.display = 'block';
            btnExplicacion.innerText = "Ocultar explicación";
        } else {
            htmlExplicacion.style.display = 'none';
            btnExplicacion.innerText = "Ver explicación paso a paso";
        }
    }

    // Iniciar el primer ejercicio al cargar la página
    generarEjercicio();

</script>

</body>
</html>
