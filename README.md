<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Examen de Lenguaje y Comunicación</title>
    <!-- Carga de Tailwind CSS desde CDN para estilos rápidos y responsivos -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Estilos personalizados para la fuente y el fondo */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f4f8; /* Un gris azulado claro para el fondo */
            display: flex;
            justify-content: center;
            align-items: flex-start; /* Alinea al inicio para que el contenido no se centre verticalmente */
            min-height: 100vh; /* Asegura que ocupe al menos toda la altura de la ventana */
            padding: 20px; /* Espaciado alrededor del contenido principal */
            box-sizing: border-box; /* Incluye padding en el ancho/alto total */
        }

        /* Contenedor principal de la aplicación */
        .app-container {
            display: flex;
            width: 100%;
            max-width: 1200px; /* Ancho máximo para el layout general */
            gap: 20px; /* Espacio entre el contenido principal y la barra lateral */
        }

        /* Estilo para las etiquetas de las opciones, para que sean clicables en toda su área */
        .option-label {
            display: block; /* Hace que la etiqueta ocupe todo el ancho disponible */
            padding: 12px 16px; /* Aumenta el área clicable */
            border-radius: 8px; /* Bordes redondeados */
            cursor: pointer; /* Cambia el cursor para indicar que es clicable */
            transition: background-color 0.2s ease, border-color 0.2s ease; /* Transición suave al pasar el ratón */
            border: 1px solid #cbd5e1; /* Borde gris claro por defecto */
            margin-bottom: 8px; /* Espacio entre opciones */
        }
        /* Estilo para la opción seleccionada */
        .option-label.selected {
            background-color: #e0f2fe; /* Azul claro para la selección */
            border-color: #3b82f6; /* Borde azul más oscuro */
            font-weight: 500; /* Texto un poco más grueso */
        }
        /* Estilo al pasar el ratón por encima de las opciones */
        .option-label:hover {
            background-color: #f0f9ff; /* Fondo muy claro al pasar el ratón */
            border-color: #93c5fd; /* Borde azul claro al pasar el ratón */
        }
        /* Oculta los radio buttons nativos */
        .option-input {
            display: none;
        }
        /* Estilo para el contenedor del mensaje (éxito/error) */
        .message-box {
            position: fixed; /* Fijo en la pantalla */
            top: 20px; /* 20px desde la parte superior */
            left: 50%; /* Centrado horizontalmente */
            transform: translateX(-50%); /* Ajuste para centrarlo perfectamente */
            background-color: #4CAF50; /* Verde para éxito */
            color: white;
            padding: 15px 25px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            z-index: 1000; /* Asegura que esté por encima de otros elementos */
            opacity: 0; /* Inicialmente oculto */
            transition: opacity 0.3s ease-in-out; /* Transición para aparecer/desaparecer */
        }
        .message-box.error {
            background-color: #f44336; /* Rojo para error */
        }
        .message-box.show {
            opacity: 1; /* Muestra el mensaje */
        }

        /* Estilos para los botones de navegación de preguntas en la barra lateral */
        .sidebar-nav-button {
            width: 40px;
            height: 40px;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 50%; /* Botones circulares */
            background-color: #e2e8f0; /* Gris claro */
            color: #475569; /* Texto gris oscuro */
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.2s ease;
            border: 1px solid #cbd5e1;
        }
        .sidebar-nav-button:hover {
            background-color: #cbd5e1;
            transform: translateY(-2px);
        }
        .sidebar-nav-button.current {
            background-color: #3b82f6; /* Azul para la pregunta actual */
            color: white;
            border-color: #3b82f6;
            box-shadow: 0 2px 4px rgba(59, 130, 246, 0.4);
        }
        .sidebar-nav-button.answered {
            background-color: #d1fae5; /* Verde claro para preguntas respondidas */
            border-color: #34d399;
        }

        /* Nuevos estilos para la retroalimentación visual en las opciones */
        .option-label.correct-answer-feedback {
            background-color: #d1fae5; /* Verde claro */
            border-color: #34d399; /* Verde */
            font-weight: 600;
        }

        .option-label.incorrect-answer-feedback {
            background-color: #fee2e2; /* Rojo claro */
            border-color: #ef4444; /* Rojo */
            font-weight: 600;
        }

        /* Media queries para responsividad */
        @media (max-width: 768px) {
            .app-container {
                flex-direction: column; /* Apila el contenido y la barra lateral en pantallas pequeñas */
                gap: 15px;
            }
            .sidebar {
                width: 100%; /* La barra lateral ocupa todo el ancho */
                order: 1; /* Coloca la barra lateral encima del contenido principal */
            }
            .main-content {
                order: 2; /* Coloca el contenido principal debajo */
            }
            body {
                padding: 10px; /* Reduce el padding general */
            }
        }
    </style>
</head>
<body class="selection:bg-blue-200">
    <div class="app-container">
        <!-- Contenido principal del simulador -->
        <div class="main-content flex-1 bg-white p-8 rounded-xl shadow-lg border border-gray-200">
            <h1 class="text-3xl font-extrabold text-gray-800 mb-6 text-center">Examen de Lenguaje y Comunicación</h1>

            <!-- Contenedor de la pregunta y opciones -->
            <div id="quiz-container">
                <div class="flex items-center text-gray-500 text-sm mb-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-red-500" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-12a1 1 0 10-2 0v4a1 1 0 102 0V6zm-1 9a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
                    </svg>
                    <span class="font-semibold">SIMULAR FINAL</span>
                </div>

                <!-- Barra de progreso -->
                <div class="mb-6">
                    <div class="w-full bg-gray-200 rounded-full h-4">
                        <div id="progress-bar" class="bg-blue-500 h-4 rounded-full transition-all duration-500 ease-out" style="width: 0%;"></div>
                    </div>
                    <p class="text-sm text-gray-500 mt-2 text-right">
                        <span id="answered-count">0</span>/<span id="total-questions-progress"></span> preguntas respondidas
                    </p>
                </div>

                <p class="text-lg font-semibold text-gray-700 mb-4" id="question-text"></p>
                <div id="options-container" class="space-y-3">
                    <!-- Las opciones se cargarán aquí dinámicamente -->
                </div>

                <!-- Controles de navegación de la página -->
                <div class="mt-8 flex justify-end items-center">
                    <button id="next-button" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-5 rounded-lg shadow-md transition duration-300 ease-in-out transform hover:scale-105">
                        Siguiente página
                    </button>
                    <button id="submit-button" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-5 rounded-lg shadow-md transition duration-300 ease-in-out transform hover:scale-105 hidden">
                        Finalizar Examen
                    </button>
                </div>
            </div>

            <!-- Contenedor de los resultados (inicialmente oculto) -->
            <div id="results-container" class="hidden mt-8 p-6 bg-blue-50 rounded-lg border border-blue-200">
                <h2 class="text-2xl font-bold text-blue-800 mb-4 text-center">Resultados del Examen</h2>
                <p class="text-xl text-gray-700 mb-2">Puntuación: <span id="score" class="font-bold text-blue-600"></span> / <span id="total-questions" class="font-bold text-blue-600"></span></p>
                <p class="text-lg text-gray-600 mb-4" id="percentage"></p>
                <div id="feedback-container" class="space-y-4">
                    <!-- El feedback de cada pregunta se cargará aquí -->
                </div>
                <button id="restart-button" class="mt-6 w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-300 ease-in-out transform hover:scale-105">
                    Reiniciar Examen
                </button>
            </div>
        </div>

        <!-- Barra lateral de navegación -->
        <div class="sidebar w-72 bg-white p-6 rounded-xl shadow-lg border border-gray-200 flex-shrink-0">
            <h3 class="text-lg font-semibold text-gray-700 mb-4">Navegación por el cuestionario</h3>
            <div id="sidebar-nav" class="grid grid-cols-4 gap-3">
                <!-- Los botones de navegación de la barra lateral se cargarán aquí dinámicamente -->
            </div>
            <button id="sidebar-submit-button" class="mt-6 w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-300 ease-in-out transform hover:scale-105">
                Terminar intento...
            </button>
        </div>
    </div>

    <!-- Contenedor para mensajes de usuario (éxito/error) -->
    <div id="message-box" class="message-box"></div>

    <script>
        // Array de objetos que contiene las preguntas, opciones y la respuesta correcta.
        // Se ha extraído la información del documento "Lenguaje final.docx".
        const questions = [
            {
                question: "Dentro de las funciones del lenguaje, el ejemplo “Juan convocó a la reunión de estudiantes” ¿A qué función pertenece de acuerdo a la clasificación propuesta por Román Jakobson?",
                options: ["Emotiva", "Fática", "Metalingüística", "Referencial"],
                correctAnswer: "Referencial"
            },
            {
                question: "¿Qué es la validación emocional?",
                options: ["Es el acercamiento entre los emisores reconociendo el lugar donde se realiza el proceso de comunicación", "Es el acercamiento, reconocimiento y aceptación del estado emocional como incidente en el proceso de comunicación", "Es la aprobación por parte de un agente externo del proceso de comunicación asertiva", "Es la confrontación de emociones entre dos interlocutores de tal manera que se obstaculiza la comunicación"],
                correctAnswer: "Es el acercamiento, reconocimiento y aceptación del estado emocional como incidente en el proceso de comunicación"
            },
            {
                question: "¿A qué denominamos como el “acto de intercambio de ideas entre dos o más individuos”?",
                options: ["Comunicación", "Lenguaje", "Lengua", "Habla"],
                correctAnswer: "Comunicación"
            },
            {
                question: "Según Karl Bühler, las funciones del lenguaje están dadas por la interacción de 3 elementos que son:",
                options: ["Audio, video y texto", "Hablante, oyente y asunto o \"cosa pensada\"", "Ruido, retroalimentación y medios", "Emisor, receptor y canal"],
                correctAnswer: "Hablante, oyente y asunto o \"cosa pensada\""
            },
            {
                question: "¿Cuál es la principal característica de la función emotiva?",
                options: ["Interrumpir un contexto comunicativo.", "Adornar o embellecer un contexto.", "Reaccionar ante una orden o pedido.", "Transmitir emociones."],
                correctAnswer: "Transmitir emociones."
            },
            {
                question: "¿Qué factores permiten una comunicación asertiva?",
                options: ["Tener un pensamiento crítico al entablar comunicación con los demás.", "Ser una persona reflexiva al entablar comunicación con los demás.", "Ser una persona positiva y respetuosa al entablar comunicación con los demás.", "Ser una persona egocéntrica al entablar comunicación con los demás."],
                correctAnswer: "Ser una persona positiva y respetuosa al entablar comunicación con los demás."
            },
            {
                question: "¿Cuál es la principal característica de la función conativa?",
                options: ["Transmitir emociones.", "Esperar una reacción ante una orden o pedido.", "Adornar o embellecer un contexto.", "Interrumpir un contexto comunicativo."],
                correctAnswer: "Esperar una reacción ante una orden o pedido."
            },
            {
                question: "Los elementos que intervienen en una comunicación son:",
                options: ["Emisor – Receptor – Metacognición – Canal – Mensaje", "Emisor – Receptor – Mensaje – Canal – Código", "Emisor – Receptor – Contraparte", "Emisor – Receptor – Mensaje – Metacognición"],
                correctAnswer: "Emisor – Receptor – Mensaje – Canal – Código"
            },
            {
                question: "¿Qué factor incide de manera directa en la escucha activa?",
                options: ["El idioma", "La especialidad de los actores", "La distancia entre los interlocutores", "La empatía"],
                correctAnswer: "La empatía"
            },
            {
                question: "El conjunto de acciones que se plasma en sonido o en escritura, al que se suma la lengua se conoce como:",
                options: ["Habla", "Acto de comunicación", "Idioma", "Lenguaje"],
                correctAnswer: "Habla"
            },
            {
                question: "¿Qué el pensamiento?",
                options: ["Es el el grupo de personas al que pertenece la religión.", "Es el acto inconsciente que no tiene ningún tipo de relación con el ser humano.", "Es la propiedad de accionar de las personas que establece la forma de abordar una situación.", "Es la actividad que realizamos a diario durante nuestro trabajo."],
                correctAnswer: "Es la propiedad de accionar de las personas que establece la forma de abordar una situación."
            },
            {
                question: "En lo que respecta a los componentes de la comunicación en el modelo transaccional, ¿cuál es el elemento clave que facilita la interacción entre emisor y receptor?",
                options: ["Fuente", "Retroalimentación", "Contexto", "Ruido"],
                correctAnswer: "Retroalimentación"
            },
            {
                question: "El modelo transaccional de comunicación, está planteado por la constante interdependencia entre la fuente de la información y los receptores. ¿Qué genera estos movimientos variables?",
                options: ["Dinámicas imprevisibles y multidireccionales", "Linealidad y homogeneidad en la comunicación", "Que se rompa la comunicación", "Que la comunicación sea solo circular"],
                correctAnswer: "Dinámicas imprevisibles y multidireccionales"
            },
            {
                question: "¿Cuáles son las dos premisas que tienen los actores de un proceso comunicativo?",
                options: ["Dormir y comer", "Escuchar y expresar", "Responder y contestar", "Reír y llorar"],
                correctAnswer: "Escuchar y expresar"
            },
            {
                question: "¿Cuál es la principal característica de la función fática?",
                options: ["Reaccionar ante una orden o pedido.", "Adornar o embellecer un contexto.", "Transmitir emociones.", "Interrumpir o iniciar un contexto comunicativo."],
                correctAnswer: "Interrumpir o iniciar un contexto comunicativo."
            },
            {
                question: "¿Cuáles son los textos argumentativos?",
                options: ["Son aquellos que poseen información específica sobre el tema que se aborde en él", "Son aquellos que cuentan con justificación para persuadir al lector", "Son relatos reales o ficticios que tienen como objetivo distraer al lector", "Son aquellos que tienen como finalidad mostrar los avances de investigación sobre un tema determinado"],
                correctAnswer: "Son aquellos que cuentan con justificación para persuadir al lector"
            },
            {
                question: "¿Qué es la atención selectiva?",
                options: ["Es el almacenamiento de la información en la memoria de corto, mediano o largo plazo", "Es el significado que le damos a un párrafo", "Es el enlace secuencial entre palabras y sus significados", "Cuando la atención está centrada en la lectura y se limitan las distracciones"],
                correctAnswer: "Cuando la atención está centrada en la lectura y se limitan las distracciones"
            },
            {
                question: "¿Qué es una lectura creadora?",
                options: ["Tiene como principal objetivo sacar conclusiones propias sobre el tema tratado en el texto.", "Es aquella donde se pone de manifiesto en la relación de identidad que se obtiene con los personajes, de simpatía y empatía", "Es donde se buscan fundamentos para que sustentes una posición a favor o en contra de lo planteado en un texto.", "Es aquella que nos lleva a la realización de cualquier actividad, con el objetivo de cambiar el sentido de lo leído hacia una nueva mirada."],
                correctAnswer: "Es aquella que nos lleva a la realización de cualquier actividad, con el objetivo de cambiar el sentido de lo leído hacia una nueva mirada."
            },
            {
                question: "¿Cuál es el nivel de lectura llamado de interpretación?",
                options: ["Es aquel donde el conocimiento recibido desde un libro es llevado a otro contexto. Implica crear.", "Es aquel que utiliza los extremos para explicar su existencia.", "Es aquel donde entra en relación lo que dice un texto y la experiencia del lector.", "Es aquel al que también se le denomina parafrasear."],
                correctAnswer: "Es aquel donde entra en relación lo que dice un texto y la experiencia del lector."
            },
            {
                question: "¿Qué condiciona de manera primordial la evaluación personal de un texto?",
                options: ["La relación parental que se tenga con el autor del texto", "El tiempo que se tenga para realizar la lectura y la posterior reflexión", "El interés que se tiene sobre el tema que aborda el texto", "El idioma en el que está escrito el texto"],
                correctAnswer: "El interés que se tiene sobre el tema que aborda el texto"
            },
            {
                question: "En la lectura crítica, ¿qué tipo de juicio es el que analiza las partes para incorporarlas y asimilarlas dentro del conocimiento propio?",
                options: ["De validez", "De adecuación", "De realidad o fantasía", "De apropiación"],
                correctAnswer: "De apropiación"
            },
            {
                question: "En la lectura crítica, ¿qué tipo de juicio es el que está condicionado por los valores éticos y morales?",
                options: ["De rechazo o aceptación", "De apropiación", "De realidad o fantasía", "De adecuación y validez"],
                correctAnswer: "De rechazo o aceptación"
            },
            {
                question: "Dentro de las estrategias de lectura, ¿a qué se refiere el límite temporal?",
                options: ["Planificar la lectura estableciendo límites de tiempo", "Buscar un lugar amplio y aseado", "Colocar un cronómetro para saber qué tiempo debemos de leer", "Acortar el tiempo de lectura saltando párrafos innecesarios"],
                correctAnswer: "Planificar la lectura estableciendo límites de tiempo"
            },
            {
                question: "¿Cuál es el nivel de lectura llamado de extrapolación?",
                options: ["Es aquel donde entra en relación lo que dice un texto y la experiencia del lector.", "Es aquel al que también se le denomina parafrasear.", "Es aquel que utiliza los extremos para explicar su existencia.", "Es aquel donde el conocimiento recibido desde un libro es llevado a otro contexto. Implica crear."],
                correctAnswer: "Es aquel donde el conocimiento recibido desde un libro es llevado a otro contexto. Implica crear."
            },
            {
                question: "¿A qué denominamos intertextualidad?",
                options: ["A la relación que hay entre lo que se lee con el conocimiento previo que tiene cada individuo y la información que se puede obtener de nuestro alrededor", "Al enlace de ideas dentro de un texto", "A la conexión de internet que está presente en todos los libros digitales", "A la coherencia y cohesión del texto con lo que piensa el autor"],
                correctAnswer: "A la relación que hay entre lo que se lee con el conocimiento previo que tiene cada individuo y la información que se puede obtener de nuestro alrededor"
            },
            {
                question: "¿Por qué es importante la lectura?",
                options: ["Porque es la base fundamental para adquirir conocimiento", "Porque la importancia está dada por el número de libros que leemos", "Porque con la lectura podemos descansar según la hora en la que leemos", "Porque así conocemos lo que pienso de uno mismo"],
                correctAnswer: "Porque es la base fundamental para adquirir conocimiento"
            },
            {
                question: "¿En qué se caracterizan los textos científicos?",
                options: ["En el lenguaje coloquial y de fácil comprensión que utiliza, sabiendo que lo puede leer cualquier público", "En el lenguaje técnico y específico que se emplea para la redacción", "En que solo debe utilizar letras, no utiliza cuadros ni gráfico ni imágenes", "Cualquier documento puede ser considerado científico. Solo depende de quién lo escribe"],
                correctAnswer: "En el lenguaje técnico y específico que se emplea para la redacción"
            },
            {
                question: "¿Cuál es el primer paso que se debe dar antes de realizar una redacción?",
                options: ["Indagación", "Planificación", "Argumentación", "Verificación"],
                correctAnswer: "Planificación"
            },
            {
                question: "En el caso de los textos académicos, 'Debe incorporar datos jerarquizados que permitan comprender el procedimiento llevado a cabo, además de los elementos claves que se rescatan de una investigación', se refiere a:",
                options: ["Monografía", "Reseña", "Ensayo", "Informe de investigación"],
                correctAnswer: "Informe de investigación"
            },
            {
                question: "Es aquella que permite diferenciar una palabra de otro que tiene la misma escritura, pero con significados diferentes. Por ejemplo: de (cuando es usada como preposición – voy de camino a casa) y dé (del verbo dar – vaya y dé la comida a los demás).",
                options: ["Acentos tónicos", "Tilde polifuncional", "Acentos atónicos", "Tilde diacrítica"],
                correctAnswer: "Tilde diacrítica"
            },
            {
                question: "¿Cuál es la estructura primaria y básica que tiene todo texto?",
                options: ["Ideas – palabras – texto – expresión oral", "Palabras – números – signos – imágenes", "Palabras – comas – puntos – firma", "Palabras – enunciados – párrafos – texto"],
                correctAnswer: "Palabras – enunciados – párrafos – texto"
            },
            {
                question: "Seleccione el enunciado correcto según el uso de los signos de puntuación",
                options: ["El punto y coma se utiliza para enumerar palabras, separar oraciones con un mismo sentido", "El punto y coma se utiliza para separar oraciones simples dentro del mismo párrafo", "El punto y coma se utiliza para separar oraciones que ya vienen separadas por una coma y requiere separar enunciados con significados diferentes.", "El punto y coma se utiliza para las palabras escritas en mayúsculas según las normas establecidas"],
                correctAnswer: "El punto y coma se utiliza para separar oraciones que ya vienen separadas por una coma y requiere separar enunciados con significados diferentes."
            },
            {
                question: "¿Para qué se utilizan los paréntesis dentro de una oración?",
                options: ["Se utilizan cuando se deja una oración o idea incompleta de manera intencional", "Se utiliza para separar oraciones que ya vienen separadas por una coma y requiere separar enunciados con significados diferentes", "Se utiliza para enunciar una pregunta, anunciando dónde inicia y dónde termina el cuestionamiento", "Se utiliza para aislar aclaraciones, datos, fechas, países o ideas"],
                correctAnswer: "Se utiliza para aislar aclaraciones, datos, fechas, países o ideas"
            },
            {
                question: "¿Cuáles son los niveles de análisis que se contempla en los comentarios de textos?",
                options: ["Nivel fónico, morfosintáctico y semántico", "Nivel literal, tácito y lingüístico", "Nivel ortográfico, caligráfico y estructural", "Nivel periodístico, argumentativo y secuencial"],
                correctAnswer: "Nivel fónico, morfosintáctico y semántico"
            },
            {
                question: "¿Cuál es el acto enriquecedor que no solo aporta conocimiento sobre aquello que generara duda e inquietudes, sino también es una puerta a nuevas ideas y sobre todo estimula a la creatividad a la hora de desarrollar un texto?",
                options: ["El habla", "La escritura", "La comunicación", "La lectura"],
                correctAnswer: "La lectura"
            },
            {
                question: "Es la interpretación y explicación libre de un tema de cualquier índole, sobre la que se ha leído y se tiene un relativo dominio al respecto. Seleccionar la respuesta correcta",
                options: ["Reseña", "Ensayo", "Monografía", "Tesis"],
                correctAnswer: "Ensayo"
            },
            {
                question: "¿En qué se clasificación los textos no literarios?",
                options: ["Textos expositivos, textos de divulgación científica, textos normativos, textos científicos", "Textos exclamativos, textos académicos, textos normativos, textos científicos", "Textos expositivos – comunicativos, textos académicos, textos normativos, textos científicos", "Textos expositivos, textos académicos, textos normativos, textos científicos"],
                correctAnswer: "Textos expositivos, textos académicos, textos normativos, textos científicos"
            },
            {
                question: "Al intentar escribir un texto, el primer obstáculo es no saber por dónde empezar, también denominado, temor a:",
                options: ["Uso de las funciones del lenguaje", "Hoja en blanco", "Uso de las reglas ortografía", "Importancia de la comunicación"],
                correctAnswer: "Hoja en blanco"
            },
            {
                question: "¿Por qué es importante el párrafo?",
                options: ["Porque hace ver más bonito el texto", "Porque contiene todos los argumentos que cimentan un texto", "Porque así podemos calificar cada una de las ideas que se presentan cuando hablamos", "Porque así es más fácil leerlo cuando lo explicamos a otras personas"],
                correctAnswer: "Porque contiene todos los argumentos que cimentan un texto"
            },
            {
                question: "El manejo de los espacios de interacción puede mostrar lo cercana o distante que es la relación entre quienes llevan a cabo una conversación, ¿a qué canal de lenguaje corporal se refiere?",
                options: ["Gestos", "Proxémica", "Háptica", "Paralenguaje"],
                correctAnswer: "Proxémica"
            },
            {
                question: "Seleccione la respuesta correcta en la definición de comunicación no verbal:",
                options: ["La comunicación no verbal mediante los símbolos transmite ideas positivas para el cambio", "La comunicación no verbal se refiere a los movimientos corporales que acompañan a la comunicación oral.", "La comunicación no verbal se utiliza para enumerar palabras, separar oraciones con un mismo sentido.", "La comunicación no verbal tiene poca relevancia dentro de un proceso de comunicación."],
                correctAnswer: "La comunicación no verbal se refiere a los movimientos corporales que acompañan a la comunicación oral."
            },
            {
                question: "¿Cuáles son los elementos principales de la oratoria?",
                options: ["Título, contenido y despedida", "Orador, contenido y público", "Saludo, presentación y despedida", "Sujeto, verbo y complemento"],
                correctAnswer: "Orador, contenido y público"
            },
            {
                question: "¿Por qué son importantes los silencios cuando hablamos en público?",
                options: ["Para dar mayor énfasis a determinadas partes del discurso", "Para poder escuchar lo que dice el público", "Para que la gente pueda aplaudir cada vez que yo hago silencio", "No hay que hacer silencio mientras se hable en público"],
                correctAnswer: "Para dar mayor énfasis a determinadas partes del discurso"
            },
            {
                question: "¿A qué se refiere la competencia discursiva?",
                options: ["A la lógica, coherencia y orden de las ideas presentadas en un discurso", "Al tono voz que se utiliza par a expresar todas las ideas en un discurso", "A los argumentos que se presentan de forma consecutiva", "Al nivel de discusión que se incentiva con un discurso"],
                correctAnswer: "A la lógica, coherencia y orden de las ideas presentadas en un discurso"
            },
            {
                question: "¿Dónde debe tener fijada la mirada una persona que interviene en público?",
                options: ["La mirada debe recorrer al público para intercambiar información.", "La mirada debe estar fija en un punto neutro para que no genere distracción.", "La mirada debe estar fija en la parte superior del espacio, el techo por ejemplo.", "La mirada debe estar fija en el piso para no perder concentración."],
                correctAnswer: "La mirada debe recorrer al público para intercambiar información."
            },
            {
                question: "¿Por qué es necesario utilizar expresiones cortas y concretas cuando hablamos en público?",
                options: ["Porque de esa manera se comprende de mejor manera el mensaje: breve y preciso.", "Para evitar mostrarme nervioso cuando estoy delante del público", "Porque de esa manera me quito el problema más rápido de encima", "Para tener que utilizar el menos tiempo posible la palabra"],
                correctAnswer: "Porque de esa manera se comprende de mejor manera el mensaje: breve y preciso."
            },
            {
                question: "¿Con qué elementos se deben elaborar los recursos de apoyo visual, como las diapositivas?",
                options: ["Debe contener enlaces a videos para evitar que tengamos que seguir hablando.", "Solo debe contener imágenes y fotografías para que se vuelva creíble lo que decimos.", "La menor cantidad de texto posible, organizada para que sirva de pauta e ilustraciones que no distraigan la atención del público.", "Con mucho texto para que sirva de guía por si se nos olvida lo que tenemos que decir literalmente."],
                correctAnswer: "La menor cantidad de texto posible, organizada para que sirva de pauta e ilustraciones que no distraigan la atención del público."
            },
            {
                question: "¿Qué elemento utiliza la comunicación escrita a través de sus distintos canales?",
                options: ["Posturas", "Gestos", "Símbolos", "Sonidos"],
                correctAnswer: "Símbolos"
            },
            {
                question: "¿Cómo deben ser nuestra gesticulación cuando hablamos en público?",
                options: ["Elocuente y marcadamente expresiva", "Debe ser enérgica y efusiva", "Debe ser totalmente natural", "No hay que hacer gesticulación alguna. Mientras menos se mueva, mejor"],
                correctAnswer: "Debe ser totalmente natural"
            },
            {
                question: "¿Cuál es el principal elemento que puede llamar la atención del público y que nos permite enfatizar en los puntos de interés en una expresión oral?",
                options: ["La mirada", "El escenario", "La voz", "El tiempo"],
                correctAnswer: "La voz"
            },
            {
                question: "En el lenguaje corporal, ¿a través de qué rasgo se evidencian las 7 emociones básicas?",
                options: ["Apariencia", "Expresiones faciales", "Posturas", "Gestos"],
                correctAnswer: "Expresiones faciales"
            },
            {
                question: "Cuando hablamos de los complementos de lenguaje, referidos al tono de voz, emociones, intenciones, ilustraciones, gráficos, etc..., ¿a qué tipo de competencia comunicativo nos referimos?",
                options: ["Paralingüística", "Pragmática", "Sociolingüística", "Lingüística"],
                correctAnswer: "Paralingüística"
            },
            {
                question: "¿Cómo se denomina al énfasis que se incorpora a la oratoria y que permite una mejor expresión de ideas?",
                options: ["Acentuación", "Pausas", "Silencios", "Dramatización"],
                correctAnswer: "Dramatización"
            },
            {
                question: "¿Qué es la atención selectiva?",
                options: ["Cuando la atención está centrada en la lectura y se limitan las distracciones", "Es el enlace secuencial entre palabras y sus significados", "Es el significado que le damos a un párrafo", "Es el almacenamiento de la información en la memoria de corto, mediano o largo plazo"],
                correctAnswer: "Cuando la atención está centrada en la lectura y se limitan las distracciones"
            },
            {
                question: "La noticia, la crónica, el reportaje ¿a qué tipo de textos pertenecen?",
                options: ["Argumentativos", "Científicos", "Humanísticos o periodísticos", "Literarios"],
                correctAnswer: "Humanísticos o periodísticos"
            },
            {
                question: "Es aquella que permite diferenciar una palabra de otro que tiene la misma escritura, pero con significados diferentes. Por ejemplo: de (cuando es usada como preposición – voy de camino a casa) y dé (del verbo dar – vaya y dé la comida a los demás).",
                options: ["Tilde polifuncional", "Tilde diacrítica", "Acentos atónicos", "Acentos tónicos"],
                correctAnswer: "Tilde diacrítica"
            },
            {
                question: "¿El nivel de lectura que tiene como principal objetivo sacar conclusiones propias sobre el tema tratado en el texto?",
                options: ["Lectura crítica", "Lectura valorativa", "Lectura creativa", "Lectura inferencial o interpretativa"],
                correctAnswer: "Lectura crítica"
            },
            {
                question: "¿Cómo debe ser el final de un texto?",
                options: ["Un resumen de cómo empezó el texto", "Lo más corto posible para que no se olvide todo lo que se ha leído", "Extenso para que se olvide todo lo leído y solo se quede retenida la conclusión", "Debe marcar el final del texto, concluir todas las ideas y argumentos desarrollados"],
                correctAnswer: "Debe marcar el final del texto, concluir todas las ideas y argumentos desarrollados"
            },
            {
                question: "¿Qué es una lectura apreciativa?",
                options: ["Es donde se buscan fundamentos para que sustentes una posición a favor o en contra de lo planteado en un texto.", "Tiene como principal objetivo sacar conclusiones propias sobre el tema tratado en el texto.", "Es aquella que nos lleva a la realización de cualquier actividad, con el objetivo de cambiar el sentido de lo leído hacia una nueva mirada.", "Es aquella donde se pone de manifiesto en la relación de identidad que se obtiene con los personajes, de simpatía y empatía."],
                correctAnswer: "Es aquella donde se pone de manifiesto en la relación de identidad que se obtiene con los personajes, de simpatía y empatía."
            },
            {
                question: "El uso de la coma (,) significa...",
                options: ["Detener una idea y centrar la atención en una idea", "Hacer una pausa breve dentro del texto", "Separar verbos dentro del mismo párrafo", "Hacer una pausa más prolongada dentro del texto"],
                correctAnswer: "Hacer una pausa breve dentro del texto"
            },
            {
                question: "¿En cuántos subniveles se divide la lectura literal o descriptiva?",
                options: ["2: primaria o básica, y secundaria o avanzada", "3: básica, intermedia y avanzada", "3: primaria, secundaria y terciaria", "2: inicial e intermedia"],
                correctAnswer: "2: primaria o básica, y secundaria o avanzada"
            }
        ];

        // Variables de estado del simulador
        let currentQuestionIndex = 0; // Índice de la pregunta actual
        // userAnswers almacena la respuesta seleccionada por el usuario para cada pregunta.
        // Si una pregunta no ha sido respondida, su valor es null.
        // Si ha sido respondida, almacena el texto de la opción seleccionada.
        const userAnswers = new Array(questions.length).fill(null);
        let score = 0; // Puntuación del usuario
        let reviewMode = false; // Nuevo flag para indicar si estamos en modo de revisión de resultados

        // Referencias a los elementos del DOM
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const nextButton = document.getElementById('next-button');
        const submitButton = document.getElementById('submit-button');
        const quizContainer = document.getElementById('quiz-container');
        const resultsContainer = document.getElementById('results-container');
        const scoreSpan = document.getElementById('score');
        const totalQuestionsSpan = document.getElementById('total-questions');
        const percentageSpan = document.getElementById('percentage');
        const feedbackContainer = document.getElementById('feedback-container');
        const restartButton = document.getElementById('restart-button');
        const messageBox = document.getElementById('message-box');
        const sidebarNav = document.getElementById('sidebar-nav');
        const sidebarSubmitButton = document.getElementById('sidebar-submit-button');
        const progressBar = document.getElementById('progress-bar');
        const answeredCountSpan = document.getElementById('answered-count');
        const totalQuestionsProgressSpan = document.getElementById('total-questions-progress');


        /**
         * Muestra un mensaje al usuario (éxito o error).
         * @param {string} message - El texto del mensaje.
         * @param {boolean} isError - True si es un mensaje de error, false para éxito.
         */
        function showMessage(message, isError = false) {
            messageBox.textContent = message;
            messageBox.className = 'message-box show'; // Resetea clases y añade 'show'

            if (isError) {
                messageBox.classList.add('error');
            } else {
                messageBox.classList.remove('error');
            }

            // Oculta el mensaje después de 3 segundos
            setTimeout(() => {
                messageBox.classList.remove('show');
            }, 3000);
        }

        /**
         * Renderiza la pregunta actual y sus opciones en la interfaz principal.
         */
        function renderQuestion() {
            const question = questions[currentQuestionIndex];
            questionText.textContent = `Pregunta ${currentQuestionIndex + 1}: ${question.question}`; // Actualiza el texto de la pregunta
            optionsContainer.innerHTML = ''; // Limpia las opciones anteriores

            // Crea y añade las opciones para la pregunta actual
            question.options.forEach((option, index) => {
                const optionId = `q${currentQuestionIndex}-option${index}`; // ID único para cada opción
                const label = document.createElement('label');
                label.htmlFor = optionId;
                label.className = `option-label bg-white text-gray-800`;
                label.innerHTML = `
                    <input type="radio" id="${optionId}" name="question${currentQuestionIndex}" value="${option}" class="option-input">
                    ${option}
                `;
                optionsContainer.appendChild(label);

                const radioInput = label.querySelector('.option-input');

                // Aplica el estado 'selected' si la respuesta ya fue seleccionada
                if (userAnswers[currentQuestionIndex] === option) {
                    label.classList.add('selected');
                    radioInput.checked = true;
                }

                // Aplica estilos de retroalimentación si ya se respondió la pregunta o si estamos en modo de revisión
                if (reviewMode || userAnswers[currentQuestionIndex] !== null) {
                    radioInput.disabled = true; // Deshabilita el input para evitar cambios

                    if (option === question.correctAnswer) {
                        label.classList.add('correct-answer-feedback'); // Marca la respuesta correcta en verde
                    }
                    // Si el usuario seleccionó esta opción Y fue incorrecta, márcala en rojo
                    if (option === userAnswers[currentQuestionIndex] && option !== question.correctAnswer) {
                        label.classList.add('incorrect-answer-feedback');
                    }
                } else {
                    // Añade un event listener a la etiqueta para manejar la selección (solo si no está en modo revisión y no respondida)
                    label.addEventListener('click', () => {
                        // Limpia estilos de selección y retroalimentación anteriores para esta pregunta
                        optionsContainer.querySelectorAll('.option-label').forEach(lbl => {
                            lbl.classList.remove('selected', 'correct-answer-feedback', 'incorrect-answer-feedback');
                        });

                        // Marca la opción seleccionada
                        label.classList.add('selected');
                        userAnswers[currentQuestionIndex] = option; // Guarda la respuesta del usuario

                        // Aplica retroalimentación visual inmediata
                        if (option === question.correctAnswer) {
                            label.classList.add('correct-answer-feedback');
                            showMessage("¡Correcto!", false);
                        } else {
                            label.classList.add('incorrect-answer-feedback');
                            showMessage("Incorrecto. La respuesta correcta es: " + question.correctAnswer, true);
                            // Opcional: Resalta la respuesta correcta si la del usuario fue incorrecta
                            optionsContainer.querySelectorAll('.option-label').forEach(lbl => {
                                if (lbl.textContent.trim() === question.correctAnswer) {
                                    lbl.classList.add('correct-answer-feedback');
                                }
                            });
                        }
                        radioInput.disabled = true; // Deshabilita el input después de la selección para evitar cambios
                        updateSidebarNavigation(); // Actualiza el estado de los botones de la barra lateral
                        updateProgressBar(); // Actualiza la barra de progreso
                    });
                }
            });

            updateNavigationButtons(); // Actualiza el estado de los botones de navegación principales
            updateSidebarNavigation(); // Actualiza el estado de los botones de la barra lateral
            updateProgressBar(); // Asegura que la barra de progreso se actualice al renderizar la pregunta
        }

        /**
         * Actualiza el estado de los botones "Siguiente página" y "Finalizar Examen".
         */
        function updateNavigationButtons() {
            // Muestra el botón "Finalizar Examen" solo en la última pregunta y si no estamos en modo revisión
            if (currentQuestionIndex === questions.length - 1 && !reviewMode) {
                nextButton.classList.add('hidden');
                submitButton.classList.remove('hidden');
            } else if (reviewMode) {
                nextButton.classList.add('hidden'); // Oculta el botón siguiente en modo revisión
                submitButton.classList.add('hidden'); // Oculta el botón finalizar en modo revisión
            } else {
                nextButton.classList.remove('hidden');
                submitButton.classList.add('hidden');
            }
        }

        /**
         * Renderiza y actualiza los botones de navegación en la barra lateral.
         */
        function updateSidebarNavigation() {
            sidebarNav.innerHTML = ''; // Limpia los botones anteriores

            questions.forEach((_, index) => {
                const navButton = document.createElement('div');
                navButton.textContent = index + 1; // Número de la pregunta
                navButton.className = 'sidebar-nav-button';

                // Añade clases para el estado visual
                if (index === currentQuestionIndex) {
                    navButton.classList.add('current'); // Pregunta actual
                }
                // Marca como respondida si el usuario ha seleccionado una opción para esta pregunta
                if (userAnswers[index] !== null) {
                    navButton.classList.add('answered');
                }

                // Añade el event listener para navegar a la pregunta al hacer clic
                navButton.addEventListener('click', () => {
                    currentQuestionIndex = index;
                    renderQuestion(); // Re-renderiza la pregunta con los estilos de revisión si aplica
                });
                sidebarNav.appendChild(navButton);
            });
        }

        /**
         * Calcula la puntuación final y muestra los resultados.
         */
        function calculateResults() {
            score = 0; // Reinicia la puntuación
            feedbackContainer.innerHTML = ''; // Limpia el feedback anterior

            questions.forEach((question, index) => {
                const userAnswer = userAnswers[index];
                const isCorrect = userAnswer === question.correctAnswer;

                if (isCorrect) {
                    score++; // Incrementa la puntuación si la respuesta es correcta
                }

                // Crea un elemento para mostrar el feedback de cada pregunta en el resumen
                const feedbackItem = document.createElement('div');
                feedbackItem.className = `p-4 rounded-lg ${isCorrect ? 'bg-green-100 border-green-300' : 'bg-red-100 border-red-300'} border`;
                feedbackItem.innerHTML = `
                    <p class="font-semibold text-gray-800">Pregunta ${index + 1}: ${question.question}</p>
                    <p class="text-gray-700 mt-2">Tu respuesta: <span class="${isCorrect ? 'text-green-600' : 'text-red-600 font-medium'}">${userAnswer || 'No respondida'}</span></p>
                    <p class="text-gray-700">Respuesta correcta: <span class="text-green-600 font-medium">${question.correctAnswer}</span></p>
                `;
                feedbackContainer.appendChild(feedbackItem);
            });

            // Muestra la puntuación y el porcentaje
            scoreSpan.textContent = score;
            totalQuestionsSpan.textContent = questions.length;
            const percentage = (score / questions.length) * 100;
            percentageSpan.textContent = `Porcentaje: ${percentage.toFixed(2)}%`;

            // Establece el modo de revisión a true
            reviewMode = true;

            // Oculta el contenedor del quiz y muestra el de resultados
            quizContainer.classList.add('hidden');
            resultsContainer.classList.remove('hidden');

            // Oculta los botones de navegación principales y el de finalizar intento de la barra lateral
            nextButton.classList.add('hidden');
            submitButton.classList.add('hidden');
            sidebarSubmitButton.classList.add('hidden');

            // Re-renderiza la barra lateral para asegurar que los botones de preguntas respondidas estén actualizados
            updateSidebarNavigation();
        }

        /**
         * Actualiza la barra de progreso y el contador de preguntas respondidas.
         */
        function updateProgressBar() {
            const answeredCount = userAnswers.filter(answer => answer !== null).length;
            const totalQuestions = questions.length;
            const percentage = (answeredCount / totalQuestions) * 100;

            progressBar.style.width = `${percentage}%`;
            answeredCountSpan.textContent = answeredCount;
            totalQuestionsProgressSpan.textContent = totalQuestions;
        }

        /**
         * Reinicia el examen a su estado inicial.
         */
        function restartExam() {
            currentQuestionIndex = 0;
            userAnswers.fill(null); // Limpia las respuestas del usuario
            score = 0; // Reinicia la puntuación
            reviewMode = false; // Restablece el modo de revisión a false

            // Oculta el contenedor de resultados y muestra el del quiz
            resultsContainer.classList.add('hidden');
            quizContainer.classList.remove('hidden');

            // Muestra los botones de navegación principales y el de finalizar intento de la barra lateral
            nextButton.classList.remove('hidden'); // Asegura que el botón siguiente sea visible para empezar
            submitButton.classList.add('hidden'); // El botón de finalizar está oculto al inicio
            sidebarSubmitButton.classList.remove('hidden');

            renderQuestion(); // Renderiza la primera pregunta
        }

        // --- Event Listeners ---

        // Botón "Siguiente página" (en el contenido principal)
        nextButton.addEventListener('click', () => {
            // Verifica si la pregunta actual ha sido respondida antes de avanzar
            if (userAnswers[currentQuestionIndex] === null) {
                showMessage("Por favor, selecciona una respuesta antes de avanzar.", true); // Muestra mensaje de error
                return; // Detiene la función si no hay respuesta
            }

            if (currentQuestionIndex < questions.length - 1) {
                currentQuestionIndex++;
                renderQuestion();
            }
        });

        // Botón "Finalizar Examen" (en el contenido principal, aparece en la última pregunta)
        submitButton.addEventListener('click', () => {
            // Verifica si la última pregunta ha sido respondida antes de finalizar
            if (userAnswers[currentQuestionIndex] === null) {
                showMessage("Por favor, selecciona una respuesta para la última pregunta antes de finalizar.", true); // Muestra mensaje de error
                return; // Detiene la función si no hay respuesta
            }
            calculateResults();
        });

        // Botón "Terminar intento..." (en la barra lateral)
        sidebarSubmitButton.addEventListener('click', () => {
            // Se podría añadir una confirmación aquí si se desea
            calculateResults();
        });

        // Botón "Reiniciar Examen" (en la pantalla de resultados)
        restartButton.addEventListener('click', restartExam);

        // Inicializa el simulador cuando la página se carga
        window.onload = renderQuestion;
    </script>
</body>
</html>
