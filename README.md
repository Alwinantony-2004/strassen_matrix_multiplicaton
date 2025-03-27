<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Strassen Matrix Multiplication</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
  <div class="min-h-screen bg-gray-100 py-8 px-4">
    <div class="max-w-4xl mx-auto">
      <div class="bg-white rounded-lg shadow-lg p-6">
        <h1 class="text-2xl font-bold text-gray-800 text-center mb-6">Strassen Matrix Multiplication</h1>

        <div class="flex flex-wrap justify-around mb-6">
          <div class="p-4">
            <h3 class="text-lg font-semibold mb-2">Matrix A</h3>
            <div id="matrixA"></div>
          </div>
          <div class="flex items-center text-2xl font-bold">×</div>
          <div class="p-4">
            <h3 class="text-lg font-semibold mb-2">Matrix B</h3>
            <div id="matrixB"></div>
          </div>
        </div>

        <div class="flex justify-center mb-6">
          <button id="calculateBtn" class="bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700 transition-colors">
            Calculate
          </button>
        </div>

        <div id="intermediateSteps" class="mt-8"></div>

        <div id="result" class="mt-8" style="display: none;">
          <h2 class="text-xl font-bold mb-4">Result:</h2>
          <div id="resultMatrix"></div>
        </div>
      </div>
    </div>
  </div>

  <script>
    function createMatrixInput(size, containerId) {
      const container = document.getElementById(containerId);
      container.innerHTML = '';
      let matrix = [];
      for (let i = 0; i < size; i++) {
        let row = [];
        const rowDiv = document.createElement('div');
        rowDiv.className = 'flex space-x-2 mb-2';
        for (let j = 0; j < size; j++) {
          const input = document.createElement('input');
          input.type = 'number';
          input.className = 'w-16 h-16 text-center border rounded';
          input.value = 0;
          row.push(input);
          rowDiv.appendChild(input);
        }
        matrix.push(row);
        container.appendChild(rowDiv);
      }
      return matrix;
    }

    function getMatrixValues(matrixInputs) {
      return matrixInputs.map(row => row.map(input => parseInt(input.value) || 0));
    }

    function strassenMultiply(A, B) {
      let [a, b, c, d] = [A[0][0], A[0][1], A[1][0], A[1][1]];
      let [e, f, g, h] = [B[0][0], B[0][1], B[1][0], B[1][1]];
      
      let M1 = (a + d) * (e + h);
      let M2 = (c + d) * e;
      let M3 = a * (f - h);
      let M4 = d * (g - e);
      let M5 = (a + b) * h;
      let M6 = (c - a) * (e + f);
      let M7 = (b - d) * (g + h);
      
      let C11 = M1 + M4 - M5 + M7;
      let C12 = M3 + M5;
      let C21 = M2 + M4;
      let C22 = M1 - M2 + M3 + M6;
      
      return { result: [[C11, C12], [C21, C22]], M1, M2, M3, M4, M5, M6, M7, C11, C12, C21, C22 };
    }

    function renderMatrix(matrix, containerId) {
      const container = document.getElementById(containerId);
      container.innerHTML = '';
      matrix.forEach(row => {
        const rowDiv = document.createElement('div');
        rowDiv.className = 'flex space-x-2';
        row.forEach(value => {
          const cell = document.createElement('div');
          cell.className = 'w-16 h-16 flex items-center justify-center border rounded bg-gray-200';
          cell.textContent = value;
          rowDiv.appendChild(cell);
        });
        container.appendChild(rowDiv);
      });
    }

    document.addEventListener('DOMContentLoaded', () => {
      const size = 2;
      const matrixAInputs = createMatrixInput(size, 'matrixA');
      const matrixBInputs = createMatrixInput(size, 'matrixB');
      
      document.getElementById('calculateBtn').addEventListener('click', () => {
        const A = getMatrixValues(matrixAInputs);
        const B = getMatrixValues(matrixBInputs);
        const { result, M1, M2, M3, M4, M5, M6, M7, C11, C12, C21, C22 } = strassenMultiply(A, B);
        
        document.getElementById('result').style.display = 'block';
        renderMatrix(result, 'resultMatrix');
        
        const stepsContainer = document.getElementById('intermediateSteps');
        stepsContainer.innerHTML = '<h2 class="text-xl font-bold mb-4">Intermediate Steps:</h2>';
        ['M1', 'M2', 'M3', 'M4', 'M5', 'M6', 'M7', 'C11', 'C12', 'C21', 'C22'].forEach(key => {
          const stepDiv = document.createElement('div');
          stepDiv.className = 'p-4';
          stepDiv.innerHTML = `<h3 class="text-lg font-semibold mb-2">${key} = ${eval(key)}</h3>`;
          stepsContainer.appendChild(stepDiv);
        });
      });
    });
  </script>
</body>
</html>
