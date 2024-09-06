<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Treino de Estatística</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            flex-direction: column;
        }
        h1 {
            color: #333;
        }
        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            max-width: 600px;
            width: 100%;
            text-align: center;
        }
        .question-container {
            margin-top: 20px;
        }
        .result {
            margin-top: 10px;
            padding: 10px;
            border-radius: 5px;
        }
        .correct {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        .incorrect {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        .explanation {
            margin-top: 10px;
            text-align: left;
            font-size: 0.9rem;
            color: #555;
            background-color: #f1f1f1;
            padding: 10px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Treino de Estatística</h1>
        <button id="generateValuesButton">Gerar Valores</button>

        <div id="questions" class="question-container"></div>
    </div>

    <script>
        const generateValuesButton = document.getElementById('generateValuesButton');
        const questionsContainer = document.getElementById('questions');

        function gerarValoresComMediaInteira() {
            const n = Math.floor(Math.random() * 6) + 5; // Número de elementos entre 5 e 10
            const mediaDesejada = Math.floor(Math.random() * 100) + 1; // Média desejada (inteira)
            const somaDesejada = mediaDesejada * n;

            let valores = [];
            for (let i = 0; i < n - 1; i++) {
                valores.push(Math.floor(Math.random() * 100) + 1);
            }

            let ultimoValor = somaDesejada - valores.reduce((a, b) => a + b, 0);

            // Regerar os valores se o último número não for válido
            while (ultimoValor < 1 || ultimoValor > 100) {
                valores = [];
                for (let i = 0; i < n - 1; i++) {
                    valores.push(Math.floor(Math.random() * 100) + 1);
                }
                ultimoValor = somaDesejada - valores.reduce((a, b) => a + b, 0);
            }

            valores.push(ultimoValor);
            return valores;
        }

        function calcularMedia(valores) {
            return valores.reduce((a, b) => a + b, 0) / valores.length;
        }

        function calcularMediana(valores) {
            valores.sort((a, b) => a - b);
            const meio = Math.floor(valores.length / 2);

            if (valores.length % 2 === 0) {
                return (valores[meio - 1] + valores[meio]) / 2;
            } else {
                return valores[meio];
            }
        }

        function calcularVariancia(valores) {
            const media = calcularMedia(valores);
            return valores.reduce((a, b) => a + (b - media) ** 2, 0) / valores.length;
        }

        function calcularDesvioPadrao(valores) {
            return Math.sqrt(calcularVariancia(valores));
        }

        function calcularDesvioMedio(valores) {
            const media = calcularMedia(valores);
            return valores.reduce((a, b) => a + Math.abs(b - media), 0) / valores.length;
        }

        function calcularCoeficienteDeVariabilidade(valores) {
            const desvioPadrao = calcularDesvioPadrao(valores);
            const media = calcularMedia(valores);
            return (desvioPadrao / media) * 100;
        }

        function criarPergunta(titulo, valorCorreto, explicacao) {
            const container = document.createElement('div');
            container.classList.add('question-container');

            const label = document.createElement('label');
            label.textContent = titulo;
            container.appendChild(label);

            const input = document.createElement('input');
            input.type = 'number';
            input.step = 'any';
            container.appendChild(input);

            const button = document.createElement('button');
            button.textContent = 'Enviar';
            button.onclick = function () {
                const resposta = parseFloat(input.value);
                const resultado = document.createElement('div');
                resultado.classList.add('result');
                const explicacaoDiv = document.createElement('div');
                explicacaoDiv.classList.add('explanation');
                explicacaoDiv.innerHTML = explicacao;

                if (resposta === parseFloat(valorCorreto.toFixed(2))) {
                    resultado.textContent = `Correto! ${titulo} é ${valorCorreto.toFixed(2)}.`;
                    resultado.classList.add('correct');
                } else {
                    resultado.textContent = `Incorreto. ${titulo} é ${valorCorreto.toFixed(2)}.`;
                    resultado.classList.add('incorrect');
                }

                container.appendChild(resultado);
                container.appendChild(explicacaoDiv);
                button.disabled = true;  // Desabilita o botão após resposta
            };
            container.appendChild(button);

            questionsContainer.appendChild(container);
        }

        generateValuesButton.onclick = function () {
            questionsContainer.innerHTML = '';  // Limpar perguntas anteriores

            const valores = gerarValoresComMediaInteira();
            const somaValores = valores.reduce((a, b) => a + b, 0);
            const media = calcularMedia(valores);
            const variancia = calcularVariancia(valores);
            const desvioPadrao = calcularDesvioPadrao(valores);
            const desvioMedio = calcularDesvioMedio(valores);
            const coefVariabilidade = calcularCoeficienteDeVariabilidade(valores);

            criarPergunta(
                `Média dos valores ${valores}`,
                media,
                `Passo a passo:<br>
                1. Soma dos valores: ${somaValores}<br>
                2. Número de valores: ${valores.length}<br>
                3. Média = Soma / Número de valores = ${somaValores} / ${valores.length} = ${media.toFixed(2)}`
            );

            criarPergunta(
                `Mediana dos valores ${valores}`,
                calcularMediana(valores),
                `Passo a passo:<br>
                1. Ordene os valores: ${valores.sort((a, b) => a - b)}<br>
                2. Mediana = valor do meio (ou média dos dois valores do meio) = ${calcularMediana(valores).toFixed(2)}`
            );

            criarPergunta(
                `Variância dos valores ${valores}`,
                variancia,
                `Passo a passo:<br>
                1. Média: ${media.toFixed(2)}<br>
                2. Para cada valor, calcule o quadrado da diferença entre ele e a média, e depois some: ${valores.map(v => `(${v} - ${media.toFixed(2)})² = ${((v - media) ** 2).toFixed(2)}`).join(", ")}<br>
                3. Variância = Soma das diferenças quadradas / Número de valores = ${variancia.toFixed(2)}`
            );

            criarPergunta(
                `Desvio Padrão dos valores ${valores}`,
                desvioPadrao,
                `Passo a passo:<br>
                1. Variância: ${variancia.toFixed(2)}<br>
                2. Desvio Padrão = Raiz quadrada da Variância = √${variancia.toFixed(2)} = ${desvioPadrao.toFixed(2)}`
            );

            criarPergunta(
                `Desvio Médio dos valores ${valores}`,
                desvioMedio,
                `Passo a passo:<br>
                1. Média: ${media.toFixed(2)}<br>
                2. Para cada valor, calcule a diferença absoluta entre ele e a média, e depois some: ${valores.map(v => `|${v} - ${media.toFixed(2)}| = ${Math.abs(v - media).toFixed(2)}`).join(", ")}<br>
                3. Desvio Médio = Soma das diferenças absolutas / Número de valores = ${desvioMedio.toFixed(2)}`
            );

            criarPergunta(
                `Coeficiente de Variabilidade dos valores ${valores}`,
                coefVariabilidade,
                `Passo a passo:<br>
                1. Desvio Padrão: ${desvioPadrao.toFixed(2)}<br>
                2. Média: ${media.toFixed(2)}<br>
                3. Coeficiente de Variabilidade = (Desvio Padrão / Média) * 100 = (${desvioPadrao.toFixed(2)} / ${media.toFixed(2)}) * 100 = ${coefVariabilidade.toFixed(2)}%`
            );
        };
    </script>
</body>
</html>
