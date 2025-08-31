<meta name="google-site-verification" content="-AJvXKWNjJolSsu8kX5VFglUcnmfourCVE6binFxq98" />
# mygames<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>اختبار تشغيل الألعاب</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="min-h-screen bg-gray-100 p-10">

  <h1 class="text-4xl font-bold text-center text-green-700 mb-8">🎮 تحقق من تشغيل الألعاب على جهازك</h1>

  <!-- مربع البحث -->
  <div class="max-w-xl mx-auto mb-6">
    <input type="text" id="searchBox" onkeyup="filterGames()" 
      class="w-full p-3 border rounded-xl shadow focus:outline-none focus:ring focus:border-blue-400" 
      placeholder="🔍 ابحث عن لعبة (مثال: GTA V)">
  </div>

  <!-- إدخال مواصفات جهاز المستخدم -->
  <div class="bg-white p-6 rounded-2xl shadow mb-8 max-w-xl mx-auto">
    <h2 class="text-xl font-semibold mb-4">💻 أدخل مواصفات جهازك:</h2>
    
    <label class="block mb-2">💾 RAM (بالـ GB):</label>
    <input type="number" id="userRam" class="w-full p-2 border rounded mb-4" placeholder="مثال: 8">

    <label class="block mb-2">⚡ CPU (عدد الأنوية):</label>
    <input type="number" id="userCpu" class="w-full p-2 border rounded mb-4" placeholder="مثال: 4">

    <label class="block mb-2">🎨 GPU (مستوى كرت الشاشة 1-10):</label>
    <input type="number" id="userGpu" class="w-full p-2 border rounded mb-4" placeholder="مثال: 6">

    <button onclick="checkGame()" class="px-6 py-2 bg-blue-600 text-white rounded-xl hover:bg-blue-700 transition">
      تحقق
    </button>
  </div>

  <!-- قائمة الألعاب -->
  <div id="gamesList" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"></div>

  <!-- النتيجة -->
  <div id="result" class="mt-10 text-center text-2xl font-bold"></div>
  <div id="download" class="mt-4 text-center"></div>

  <script>
    let selectedGame = null;
    const games = {
      "Fortnite": { ram: 8, cpu: 4, gpu: 6, link: "https://store.epicgames.com/p/fortnite", free: true, img: "https://cdn2.unrealengine.com/fortnite-og-social-1920x1080-5f021a6c25d0.jpg" },
      "Valorant": { ram: 4, cpu: 2, gpu: 4, link: "https://playvalorant.com", free: true, img: "https://images.contentstack.io/v3/assets/bltb6530b271fddd0b1/blt0725d6be3c94adf4/60ee0bfc527cc20f5b6a9a3f/VALORANT_Ep3-Act1_KeyArt_16-9.jpg" },
      "Apex Legends": { ram: 6, cpu: 4, gpu: 7, link: "https://store.steampowered.com/app/1172470/Apex_Legends/", free: true, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/1172470/header.jpg" },
      "Minecraft": { ram: 4, cpu: 2, gpu: 3, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/813780/header.jpg" },
      "GTA V": { ram: 8, cpu: 4, gpu: 7, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/271590/header.jpg" },
      "The Witcher 3": { ram: 6, cpu: 4, gpu: 6, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/292030/header.jpg" },
      "Among Us": { ram: 2, cpu: 1, gpu: 2, link: "https://store.steampowered.com/app/945360/Among_Us/", free: true, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/945360/header.jpg" },
      "Roblox": { ram: 2, cpu: 1, gpu: 2, link: "https://www.roblox.com/download", free: true, img: "https://tr.rbxcdn.com/5e17da9dbbc00fbf6d59f39a9706ac94/768/432/Image/Webp" },
      "Stardew Valley": { ram: 2, cpu: 2, gpu: 2, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/413150/header.jpg" },
      "Dota 2": { ram: 4, cpu: 2, gpu: 4, link: "https://store.steampowered.com/app/570/Dota_2/", free: true, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/570/header.jpg" },
      "Forza Horizon 5": { ram: 16, cpu: 6, gpu: 8, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/1551360/header.jpg" },
      "Red Dead Redemption 2": { ram: 12, cpu: 6, gpu: 8, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/1174180/header.jpg" },
      "The Last of Us Part I": { ram: 16, cpu: 8, gpu: 9, link: "", free: false, img: "https://cdn.cloudflare.steamstatic.com/steam/apps/1888930/header.jpg" }
    };

    // بناء البطاقات
    function renderGames(filter = "") {
      const gamesContainer = document.getElementById("gamesList");
      gamesContainer.innerHTML = "";
      let found = false;
      for (let g in games) {
        if (g.toLowerCase().includes(filter.toLowerCase())) {
          let game = games[g];
          gamesContainer.innerHTML += `
            <div onclick="selectGame('${g}')" class="bg-white p-6 rounded-2xl shadow hover:shadow-lg transition cursor-pointer">
              <img src="${game.img}" class="rounded-xl mb-4">
              <h2 class="text-xl font-semibold mb-2">${g}</h2>
              <p class="text-gray-600">RAM ${game.ram}GB - CPU ${game.cpu} - GPU ${game.gpu}</p>
              <p class="${game.free ? 'text-green-600' : 'text-red-600'} font-bold">
                ${game.free ? "مجاني ✅" : "مدفوع 💲"}
              </p>
            </div>
          `;
          found = true;
        }
      }
      if (!found) {
        gamesContainer.innerHTML = `<p class="text-center text-red-600 text-xl font-bold">⚠️ اللعبة غير موجودة في قائمتنا</p>`;
      }
    }

    // أول تحميل
    renderGames();

    // البحث
    function filterGames() {
      let query = document.getElementById("searchBox").value;
      renderGames(query);
    }

    // اختيار لعبة
    function selectGame(game) {
      selectedGame = game;
      document.getElementById("result").innerHTML = "✅ اخترت اللعبة: " + game + ". الآن أدخل مواصفات جهازك واضغط تحقق.";
      document.getElementById("result").className = "mt-10 text-center text-xl font-bold text-blue-600";
      document.getElementById("download").innerHTML = "";
    }

    // التحقق من التشغيل
    function checkGame() {
      if (!selectedGame) {
        alert("من فضلك اختر لعبة أولاً!");
        return;
      }

      let userRam = parseInt(document.getElementById("userRam").value);
      let userCpu = parseInt(document.getElementById("userCpu").value);
      let userGpu = parseInt(document.getElementById("userGpu").value);

      if (!userRam || !userCpu || !userGpu) {
        alert("أدخل كل المواصفات من فضلك!");
        return;
      }

      let req = games[selectedGame];
      if (userRam >= req.ram && userCpu >= req.cpu && userGpu >= req.gpu) {
        if (req.free) {
          document.getElementById("result").innerHTML = "🎉 جهازك قادر على تشغيل " + selectedGame + " ✅";
          document.getElementById("result").className = "mt-10 text-center text-2xl font-bold text-green-600";
          document.getElementById("download").innerHTML = 
            `<a href="${req.link}" target="_blank" class="px-6 py-2 bg-green-600 text-white rounded-xl hover:bg-green-700 transition">⬇️ تحميل ${selectedGame}</a>`;
        } else {
          document.getElementById("result").innerHTML = "💲 جهازك قادر على تشغيل " + selectedGame + " ✅ لكن اللعبة مدفوعة.";
          document.getElementById("result").className = "mt-10 text-center text-2xl font-bold text-yellow-600";
          document.getElementById("download").innerHTML = "";
        }
      } else {
        document.getElementById("result").innerHTML = "❌ جهازك لا يستطيع تشغيل " + selectedGame + " بكفاءة.";
        document.getElementById("result").className = "mt-10 text-center text-2xl font-bold text-red-600";
        document.getElementById("download").innerHTML = "";
      }
    }
  </script>

</body>
</html>
