<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Magic Chess Mini</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(120deg, #1d1f27, #2b2d3c);
      color: white;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }

    h1 {
      margin-bottom: 10px;
      animation: fadeIn 1s ease-out;
    }

    #board {
      display: grid;
      grid-template-columns: repeat(6, 60px);
      grid-template-rows: repeat(6, 60px);
      gap: 5px;
      margin-top: 20px;
    }

    .cell {
      width: 60px;
      height: 60px;
      background: #444;
      border: 2px solid #666;
      border-radius: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      transition: transform 0.2s;
    }

    .cell:hover {
      transform: scale(1.05);
    }

    .piece {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      animation: drop 0.3s ease-out;
    }

    .p1 {
      background: linear-gradient(to bottom right, #00c3ff, #005eff);
    }

    .p2 {
      background: linear-gradient(to bottom right, #ff6b6b, #c0392b);
    }

    .explode {
      animation: explode 0.5s forwards;
    }

    @keyframes drop {
      from { transform: scale(0); opacity: 0; }
      to { transform: scale(1); opacity: 1; }
    }

    @keyframes explode {
      0% { transform: scale(1); opacity: 1; }
      100% { transform: scale(2); opacity: 0; }
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20px); }
      to { opacity: 1; transform: translateY(0); }
    }

    #turnDisplay {
      margin-top: 15px;
      font-size: 18px;
    }

    #winner {
      margin-top: 20px;
      font-size: 22px;
      font-weight: bold;
      color: yellow;
    }
  </style>
</head>
<body>
  <h1>Magic Chess Mini</h1>
  <div id="turnDisplay">Giliran: Pemain 1 (Biru)</div>
  <div id="board"></div>
  <div id="winner"></div>

  <script>
    const board = document.getElementById("board");
    const turnDisplay = document.getElementById("turnDisplay");
    const winnerDisplay = document.getElementById("winner");
    let currentPlayer = 1;
    const cells = [];

    for (let i = 0; i < 36; i++) {
      const cell = document.createElement("div");
      cell.classList.add("cell");
      cell.dataset.index = i;
      cell.addEventListener("click", () => placePiece(cell));
      board.appendChild(cell);
      cells.push(cell);
    }

    function placePiece(cell) {
      if (cell.children.length > 0 || winnerDisplay.textContent) return;

      const piece = document.createElement("div");
      const className = currentPlayer === 1 ? "p1" : "p2";
      piece.classList.add("piece", className);
      cell.appendChild(piece);

      attackAround(cell, className);
      checkWinner();

      currentPlayer = currentPlayer === 1 ? 2 : 1;
      turnDisplay.textContent = `Giliran: Pemain ${currentPlayer} (${currentPlayer === 1 ? "Biru" : "Merah"})`;
    }

    function attackAround(cell, playerClass) {
      const index = parseInt(cell.dataset.index);
      const directions = [-6, 6, -1, 1]; // atas, bawah, kiri, kanan

      directions.forEach(dir => {
        const targetIndex = index + dir;
        if (targetIndex < 0 || targetIndex >= 36) return;

        // Pastikan tidak nyerang lewat batas kiri/kanan
        if (dir === -1 && index % 6 === 0) return;
        if (dir === 1 && index % 6 === 5) return;

        const targetCell = cells[targetIndex];
        if (targetCell.children.length > 0) {
          const targetPiece = targetCell.children[0];
          if (!targetPiece.classList.contains(playerClass)) {
            targetPiece.classList.add("explode");
            setTimeout(() => {
              targetCell.removeChild(targetPiece);
              checkWinner();
            }, 400);
          }
        }
      });
    }

    function checkWinner() {
      const pieces = document.querySelectorAll(".piece");
      let p1Count = 0, p2Count = 0;
      pieces.forEach(p => {
        if (p.classList.contains("p1")) p1Count++;
        if (p.classList.contains("p2")) p2Count++;
      });

      if (p1Count === 0 && p2Count > 0) {
        winnerDisplay.textContent = "Pemain 2 (Merah) MENANG!";
      } else if (p2Count === 0 && p1Count > 0) {
        winnerDisplay.textContent = "Pemain 1 (Biru) MENANG!";
      } else if (p1Count + p2Count === 36) {
        winnerDisplay.textContent = "Permainan Seri!";
      }
    }
  </script>
</body>
</html>
