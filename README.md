<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Caja de la Suerte</title>
  <style>
    body { font-family: Arial; background: #f8f8f8; padding: 20px; }
    button { padding: 10px 20px; margin: 10px; }
    .item { margin: 5px 0; }
    #inventory, #money { margin-top: 20px; }
  </style>
</head>
<body>

  <h1>Caja de la Suerte</h1>
  <button onclick="openBox()">Abrir Caja</button>
  <button onclick="collectMoney()">Cobrar Dinero</button>
  <button onclick="craft('ring')">Fabricar Anillo de la Suerte</button>
  <button onclick="craft('clover')">Fabricar Trébol de 4 Hojas</button>
  <button onclick="craft('block')">Fabricar Bloque de la Suerte</button>
  <button onclick="craft('toilet')">Fabricar Baño Dorado</button>

  <div id="result"></div>
  <div id="money">Dinero: 0</div>

  <h3>Inventario:</h3>
  <div id="inventory"></div>

  <script>
    let inventory = {};
    let money = 0;
    let bonus = 0;
    let lastCollect = Date.now();

    const rarities = [
      { name: "Súper Común", chance: 0.5 },
      { name: "Común", chance: 1/3 },
      { name: "Raro", chance: 1/5 },
      { name: "Súper Raro", chance: 1/10 },
      { name: "Rarísimo", chance: 1/25 },
      { name: "Épico", chance: 1/50 },
      { name: "Legendario", chance: 1/100 },
      { name: "Mítico", chance: 1/500 },
      { name: "Demonio", chance: 1/1000 },
      { name: "Suerte", chance: 1/5000 },
      { name: "Súper Suertudo", chance: 1/10000 },
      { name: "Súper Imposible", chance: 1/50000 },
      { name: "Ultra Imposible", chance: 1/100000 },
      { name: "Papel Misterioso", chance: 1/200000 }
    ];

    function getRandomRarity() {
      let rand = Math.random();
      let total = 0;
      for (let r of rarities) total += 1 / r.chance;
      let roll = Math.random() * total;
      let acc = 0;
      for (let r of rarities) {
        acc += 1 / r.chance;
        if (roll <= acc) return r.name;
      }
      return "Común"; // fallback
    }

    function openBox() {
      let luckMultiplier = 1 + bonus;
      let item = getRandomRarity();
      inventory[item] = (inventory[item] || 0) + 1;
      document.getElementById("result").innerText = `¡Obtuviste: ${item}!`;
      updateInventory();
    }

    function updateInventory() {
      let out = "";
      for (let item in inventory) {
        out += `<div class='item'>${item}: ${inventory[item]}</div>`;
      }
      document.getElementById("inventory").innerHTML = out;
    }

    function collectMoney() {
      let now = Date.now();
      let minutes = Math.floor((now - lastCollect) / 60000);
      let bonusIncome = inventory["Baño Dorado"] ? inventory["Baño Dorado"] * 100 * minutes : 0;
      money += bonusIncome;
      lastCollect = now;
      document.getElementById("money").innerText = `Dinero: ${money}`;
    }

    function craft(type) {
      if (type === "ring") {
        if ((inventory["Común"] || 0) >= 5 && (inventory["Raro"] || 0) >= 1) {
          inventory["Común"] -= 5;
          inventory["Raro"] -= 1;
          inventory["Anillo de la Suerte"] = (inventory["Anillo de la Suerte"] || 0) + 1;
          bonus += 0.5;
          alert("¡Fabricaste un Anillo de la Suerte! (+50% suerte)");
        } else alert("Faltan materiales.");
      }

      if (type === "clover") {
        if ((inventory["Raro"] || 0) >= 5 && (inventory["Rarísimo"] || 0) >= 1) {
          inventory["Raro"] -= 5;
          inventory["Rarísimo"] -= 1;
          inventory["Trébol de 4 Hojas"] = (inventory["Trébol de 4 Hojas"] || 0) + 1;
          bonus += 1.0;
          alert("¡Fabricaste un Trébol de 4 Hojas! (+100% suerte)");
        } else alert("Faltan materiales.");
      }

      if (type === "block") {
        if ((inventory["Común"] || 0) >= 50 && (inventory["Raro"] || 0) >= 5 && (inventory["Rarísimo"] || 0) >= 1 && (inventory["Épico"] || 0) >= 1) {
          inventory["Común"] -= 50;
          inventory["Raro"] -= 5;
          inventory["Rarísimo"] -= 1;
          inventory["Épico"] -= 1;
          let chance = Math.random();
          if (chance < 0.66) {
            inventory["Anillo de la Suerte"] = (inventory["Anillo de la Suerte"] || 0) + 1;
            bonus += 0.5;
            alert("¡Del bloque salió un Anillo de la Suerte!");
          } else if (chance < 0.96) {
            inventory["Trébol de 4 Hojas"] = (inventory["Trébol de 4 Hojas"] || 0) + 1;
            bonus += 1.0;
            alert("¡Del bloque salió un Trébol de 4 Hojas!");
          } else {
            inventory["Papel Misterioso"] = 1;
            alert("¡Del bloque salió un misterioso PAPEL! Descripción: ???");
          }
        } else alert("Faltan materiales.");
      }

      if (type === "toilet") {
        if ((inventory["Épico"] || 0) >= 5 && (inventory["Legendario"] || 0) >= 1) {
          inventory["Épico"] -= 5;
          inventory["Legendario"] -= 1;
          inventory["Baño Dorado"] = (inventory["Baño Dorado"] || 0) + 1;
          bonus += 0.05;
          alert("¡Fabricaste un Baño Dorado! (+5% suerte, +100 monedas/min)");
        } else alert("Faltan materiales.");
      }

      updateInventory();
      document.getElementById("money").innerText = `Dinero: ${money}`;
    }

    // Papel secreto
    document.body.addEventListener("click", (e) => {
      if (inventory["Papel Misterioso"]) {
        alert("¡Has tocado el Papel Misterioso! Te transporta a un lugar suertudo...");
        window.location.href = "https://www.random.org"; // Puedes cambiarlo a lo que quieras
      }
    });
  </script>

</body>
</html>
