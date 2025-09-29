<!DOCTYPE html>
<html lang="ko">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>마인크래프트 광질 게임</title>
   <script src="https://cdn.tailwindcss.com"></script>
   <style>
       @import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');
       body {
           font-family: 'VT323', monospace;
           background-color: #1a1a1a;
           color: #ffffff;
           display: flex;
           justify-content: center;
           align-items: center;
           min-height: 100vh;
           margin: 0;
           flex-direction: row;
           flex-wrap: wrap;
       }
       .main-container {
           display: flex;
           flex-direction: row;
           gap: 20px;
           justify-content: center;
           align-items: flex-start;
       }
       .game-container {
           border: 4px solid #4a4a4a;
           box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
           background-color: #333;
           border-radius: 12px;
           padding: 20px;
           text-align: center;
           display: flex;
           flex-direction: column;
           align-items: center;
       }
       canvas {
           background-color: #222;
           image-rendering: pixelated;
           border: 2px solid #555;
           border-radius: 8px;
           margin-bottom: 20px;
       }
       .controls {
           margin-top: 10px;
       }
       .controls p {
           font-size: 1.2rem;
           margin: 5px 0;
       }
       .message-box {
           background-color: #4a4a4a;
           border: 2px solid #555;
           border-radius: 8px;
           padding: 10px 20px;
           min-height: 50px;
           display: flex;
           justify-content: center;
           align-items: center;
           font-size: 1.2rem;
           margin-top: 10px;
       }
       .score-box {
           background-color: #4a4a4a;
           border: 2px solid #555;
           border-radius: 8px;
           padding: 10px 20px;
           margin-bottom: 10px;
           font-size: 1.5rem;
       }
       .inventory-box {
           background-color: #4a4a4a;
           border: 2px solid #555;
           border-radius: 8px;
           padding: 10px 20px;
           margin-top: 10px;
           font-size: 1.2rem;
       }
       .chat-container {
           width: 300px;
           background-color: #2a2a2a;
           border: 4px solid #4a4a4a;
           border-radius: 12px;
           padding: 20px;
           display: flex;
           flex-direction: column;
           justify-content: space-between;
           box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
       }
       .chat-header {
           font-size: 1.5rem;
           font-weight: bold;
           text-align: center;
           margin-bottom: 10px;
       }
       .chat-messages {
           flex-grow: 1;
           overflow-y: auto;
           max-height: 300px;
           padding: 10px;
           background-color: #1a1a1a;
           border-radius: 8px;
           display: flex;
           flex-direction: column;
           gap: 8px;
       }
       .chat-message {
           background-color: #444;
           padding: 8px;
           border-radius: 6px;
           word-wrap: break-word;
       }
       .chat-message-user {
           font-size: 0.8rem;
           color: #bbb;
       }
       .chat-message-npc {
           background-color: #4a4a4a;
           font-size: 0.9rem;
           color: #d8b4fe;
           font-style: italic;
       }
       .chat-input-container {
           display: flex;
           gap: 10px;
           margin-top: 10px;
       }
       .chat-input {
           flex-grow: 1;
           padding: 8px;
           background-color: #1a1a1a;
           border: 2px solid #555;
           border-radius: 6px;
           color: white;
       }
       .chat-send-btn {
           padding: 8px 12px;
           background-color: #3498db;
           color: white;
           border-radius: 6px;
           cursor: pointer;
           border: none;
       }
       .game-over-overlay {
           position: absolute;
           top: 0;
           left: 0;
           width: 100%;
           height: 100%;
           background-color: rgba(0, 0, 0, 0.7);
           display: flex;
           justify-content: center;
           align-items: center;
           flex-direction: column;
           z-index: 10;
       }
       .game-over-text {
           font-size: 3rem;
           font-weight: bold;
           color: #2ecc71;
           text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
       }
       .game-over-buttons {
           display: flex;
           gap: 20px;
           margin-top: 20px;
       }
       .game-over-button {
           padding: 10px 20px;
           font-size: 1.2rem;
           border-radius: 8px;
           cursor: pointer;
           transition: transform 0.2s, box-shadow 0.2s;
       }
       .game-over-button:hover {
           transform: translateY(-2px);
           box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
       }
       #playAgainBtn {
           background-color: #2ecc71;
           color: white;
           border: none;
       }
       #quitBtn {
           background-color: #e74c3c;
           color: white;
           border: none;
       }
   </style>
</head>
<body>


<div class="main-container">
   <div class="game-container" style="position: relative;">
       <h1 class="text-4xl font-bold mb-4">마인크래프트 광질 게임</h1>
       <div class="score-box">
           점수: <span id="score">0</span>
       </div>
       <canvas id="gameCanvas" width="800" height="400"></canvas>
       <div class="controls">
           <p><strong>이동:</strong> 화살표 키 (↑, ↓, ←, →)</p>
           <p><strong>채굴:</strong> 스페이스바</p>
           <p><strong>화면 이동:</strong> 마우스 오른쪽 버튼 드래그</p>
           <p><strong>상점 이용:</strong> 상인 옆에서 스페이스바</p>
           <p class="mt-2 text-yellow-400"><strong>✨ 상인에게 말하기:</strong> 채팅창에 '상인: [하고 싶은 말]' 입력</p>
       </div>
       <div class="message-box" id="messageBox"></div>
       <div class="inventory-box">
           인벤토리: <span id="inventory"></span>
       </div>
       <div id="gameOverOverlay" class="game-over-overlay hidden" style="display: none;">
           <div class="game-over-text">당신은 가뭄을 해결했습니다</div>
           <div class="game-over-buttons">
               <button id="playAgainBtn" class="game-over-button">다시 플레이</button>
               <button id="quitBtn" class="game-over-button">게임 그만하기</button>
           </div>
       </div>
   </div>
  
   <div class="chat-container">
       <div class="chat-header">채팅</div>
       <div class="chat-messages" id="chatMessages"></div>
       <div class="chat-input-container">
           <input type="text" id="chatInput" class="chat-input" placeholder="메시지 입력...">
           <button id="sendBtn" class="chat-send-btn">보내기</button>
       </div>
       <div class="text-center text-xs mt-2 text-gray-400">
           당신의 ID: <span id="userIdDisplay">연결 중...</span>
       </div>
   </div>
</div>


<script type="module">
   import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
   import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
   import { getFirestore, collection, addDoc, onSnapshot, query, orderBy, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
  
   // Global variables for Firebase setup
   const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
   const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
   const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
  
   // Gemini API settings
   const apiKey = "";
   const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
   const systemPrompt = "당신은 '올드 맨 스티브'라는 이름의 친절하고 현명하며 약간 괴짜 같은 광산 세계의 상인입니다. 당신은 가뭄을 해결하기 위해 물을 판매합니다. 당신의 답변은 짧고 격려하며, 채굴, 게임 세계, 그리고 가뭄과 관련되어야 합니다. 당신이 AI나 언어 모델이라는 것을 언급하지 마십시오.";




   // Initialize Firebase
   const app = initializeApp(firebaseConfig);
   const db = getFirestore(app);
   const auth = getAuth(app);


   let currentUserId = null;
   const chatCollectionPath = `/artifacts/${appId}/public/data/chat`;
   const userIdDisplay = document.getElementById('userIdDisplay');
   const chatMessagesContainer = document.getElementById('chatMessages');
   const chatInput = document.getElementById('chatInput');
   const sendBtn = document.getElementById('sendBtn');
  
   // Authenticate and set up chat listener
   onAuthStateChanged(auth, async (user) => {
       if (user) {
           currentUserId = user.uid;
           userIdDisplay.textContent = currentUserId.slice(0, 8) + '...';
           setupChatListener();
       } else {
           if (initialAuthToken) {
               try {
                   await signInWithCustomToken(auth, initialAuthToken);
               } catch (error) {
                   console.error("Custom token sign-in failed:", error);
                   await signInAnonymously(auth);
               }
           } else {
               await signInAnonymously(auth);
           }
       }
   });


   // Function to add a new message to Firestore
   const sendMessage = async () => {
       const messageText = chatInput.value.trim();
       if (messageText === "" || !currentUserId) return;


       // Check if the message is for the NPC
       if (messageText.startsWith("상인:")) {
           const userQuery = messageText.substring(3).trim();
           await getShopkeeperResponse(userQuery);
       } else {
           // Regular chat message to Firestore
           try {
               await addDoc(collection(db, chatCollectionPath), {
                   userId: currentUserId,
                   text: messageText,
                   timestamp: serverTimestamp()
               });
           } catch (e) {
               console.error("Error adding message: ", e);
           }
       }
       chatInput.value = "";
   };


   // Listen for new messages in real-time
   const setupChatListener = () => {
       const q = query(collection(db, chatCollectionPath), orderBy("timestamp"));
       onSnapshot(q, (snapshot) => {
           chatMessagesContainer.innerHTML = '';
           snapshot.forEach((doc) => {
               const data = doc.data();
               const messageElement = document.createElement('div');
               messageElement.className = 'chat-message';
               const messageUser = data.userId.slice(0, 8) + '...';
               messageElement.innerHTML = `<span class="chat-message-user">${messageUser}:</span> ${data.text}`;
               chatMessagesContainer.appendChild(messageElement);


               // 채팅 메시지에서 공백을 제거하고 '상점'인지 확인하여 순간이동
               if (data.text.trim() === '상점' && data.userId === currentUserId) {
                   player.x = shopkeeper.x;
                   player.y = shopkeeper.y + 1; // Teleport just below the shopkeeper
                   draw();
                   showMessage("상점으로 순간이동합니다!");
               }
           });
           chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;
       });
   };
  
   // Function to get a response from the Gemini API
   const getShopkeeperResponse = async (userQuery) => {
       const messageElement = document.createElement('div');
       messageElement.className = 'chat-message chat-message-npc';
       messageElement.innerHTML = `상인: ...생각 중...`;
       chatMessagesContainer.appendChild(messageElement);
       chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;


       try {
           const payload = {
               contents: [{ parts: [{ text: userQuery }] }],
               systemInstruction: { parts: [{ text: systemPrompt }] },
               tools: [{ "google_search": {} }]
           };
           const response = await fetch(apiUrl, {
               method: 'POST',
               headers: { 'Content-Type': 'application/json' },
               body: JSON.stringify(payload)
           });
           const result = await response.json();
           const npcText = result.candidates?.[0]?.content?.parts?.[0]?.text || "죄송합니다, 지금은 답변해드릴 수 없습니다.";
          
           messageElement.innerHTML = `상인: ${npcText}`;


       } catch (error) {
           console.error("Gemini API call failed:", error);
           messageElement.innerHTML = `상인: 죄송합니다, 지금은 답변해드릴 수 없습니다.`;
       }
       chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;
   };




   sendBtn.addEventListener('click', sendMessage);
   chatInput.addEventListener('keydown', (e) => {
       if (e.key === 'Enter') {
           sendMessage();
       }
   });


   const canvas = document.getElementById('gameCanvas');
   const ctx = canvas.getContext('2d');
   const messageBox = document.getElementById('messageBox');
   const scoreElement = document.getElementById('score');
   const inventoryElement = document.getElementById('inventory');
   const gameOverOverlay = document.getElementById('gameOverOverlay');
   const playAgainBtn = document.getElementById('playAgainBtn');
   const quitBtn = document.getElementById('quitBtn');


   const TILE_SIZE = 40;
   const GRID_WIDTH = canvas.width / TILE_SIZE;
   const GRID_HEIGHT = canvas.height / TILE_SIZE;


   const MAP_WIDTH = 200;
   const MAP_HEIGHT = 100;


   let world = [];
   let player = {};
   let shopkeeper = {};
   let score = 0;
   let isGameOver = false;
  
   // 마우스 드래그를 위한 변수
   let isDragging = false;
   let lastMouseX = 0;
   let lastMouseY = 0;


   const TILE_TYPES = {
       EMPTY: 0,
       STONE: 1,
       COAL: 2,
       IRON: 3,
       GOLD: 4,
       DIAMOND: 5,
       LAPIS: 6,
       REDSTONE: 7,
       EMERALD: 8,
       SAPPHIRE: 9,
       RUBY: 10
   };


   const TILE_COLORS = {
       [TILE_TYPES.EMPTY]: '#343434',
       [TILE_TYPES.STONE]: '#5c5c5c',
       [TILE_TYPES.COAL]: '#4d4d4d',
       [TILE_TYPES.IRON]: '#c0c0c0',
       [TILE_TYPES.GOLD]: '#ffd700',
       [TILE_TYPES.DIAMOND]: '#00ced1',
       [TILE_TYPES.LAPIS]: '#1a237e',
       [TILE_TYPES.REDSTONE]: '#b71c1c',
       [TILE_TYPES.EMERALD]: '#4caf50',
       [TILE_TYPES.SAPPHIRE]: '#0d47a1',
       [TILE_TYPES.RUBY]: '#d50000'
   };


   const TILE_SCORES = {
       [TILE_TYPES.COAL]: 1,
       [TILE_TYPES.IRON]: 5,
       [TILE_TYPES.GOLD]: 20,
       [TILE_TYPES.DIAMOND]: 100,
       [TILE_TYPES.LAPIS]: 2,
       [TILE_TYPES.REDSTONE]: 10,
       [TILE_TYPES.EMERALD]: 50,
       [TILE_TYPES.SAPPHIRE]: 80,
       [TILE_TYPES.RUBY]: 120
   };


   function resetGame() {
       score = 0;
       isGameOver = false;
       player = {
           x: 1,
           y: 1,
           color: '#3498db',
           direction: 'right',
           inventory: {}
       };
       shopkeeper = {
           x: 5,
           y: 5,
           color: '#2ecc71'
       };
       scoreElement.textContent = score;
       gameOverOverlay.style.display = 'none';
       generateCaveMap();
       draw();
       updateInventory();
       showMessage("게임이 재시작되었습니다!");
   }


   function drawStoneTexture(x, y, tileSize) {
       ctx.fillStyle = '#5c5c5c';
       ctx.fillRect(x, y, tileSize, tileSize);
      
       for (let i = 0; i < 50; i++) {
           const grainX = x + Math.random() * tileSize;
           const grainY = y + Math.random() * tileSize;
           ctx.fillStyle = `rgba(0, 0, 0, ${0.1 + Math.random() * 0.3})`;
           ctx.fillRect(grainX, grainY, 1, 1);
       }


       for (let i = 0; i < 20; i++) {
           const grainX = x + Math.random() * tileSize;
           const grainY = y + Math.random() * tileSize;
           ctx.fillStyle = `rgba(255, 255, 255, ${0.1 + Math.random() * 0.3})`;
           ctx.fillRect(grainX, grainY, 1, 1);
       }
   }


   function drawOreTexture(x, y, tileSize, oreType) {
       drawStoneTexture(x, y, tileSize);
      
       let oreColor;
       let numOre;
       let oreSize;


       switch(oreType) {
           case TILE_TYPES.COAL:
               oreColor = '#111111';
               numOre = 15;
               oreSize = 4;
               break;
           case TILE_TYPES.IRON:
               oreColor = '#c0c0c0';
               numOre = 8;
               oreSize = 5;
               break;
           case TILE_TYPES.GOLD:
               oreColor = '#ffd700';
               numOre = 6;
               oreSize = 6;
               break;
           case TILE_TYPES.DIAMOND:
               oreColor = '#00ced1';
               numOre = 3;
               oreSize = 8;
               break;
           case TILE_TYPES.LAPIS:
               oreColor = '#1a237e';
               numOre = 12;
               oreSize = 4;
               break;
           case TILE_TYPES.REDSTONE:
               oreColor = '#b71c1c';
               numOre = 20;
               oreSize = 2;
               break;
           case TILE_TYPES.EMERALD:
               oreColor = '#4caf50';
               numOre = 5;
               oreSize = 7;
               break;
           case TILE_TYPES.SAPPHIRE:
               oreColor = '#0d47a1';
               numOre = 4;
               oreSize = 8;
               break;
           case TILE_TYPES.RUBY:
               oreColor = '#d50000';
               numOre = 2;
               oreSize = 10;
               break;
       }


       for (let i = 0; i < numOre; i++) {
           const oreX = x + (Math.random() * (tileSize - oreSize));
           const oreY = y + (Math.random() * (tileSize - oreSize));
           ctx.fillStyle = oreColor;
           ctx.fillRect(oreX, oreY, oreSize, oreSize);
       }
   }


   function generateCaveMap() {
       // Initialize the world map with stone tiles
       world = [];
       for (let y = 0; y < MAP_HEIGHT; y++) {
           const row = new Array(MAP_WIDTH).fill(TILE_TYPES.STONE);
           world.push(row);
       }


       // 입구 생성
       for (let y = 0; y < MAP_HEIGHT; y++) {
           for (let x = 0; x < 20; x++) {
               world[y][x] = TILE_TYPES.EMPTY;
           }
       }
       // 상점 위치의 블록을 빈 공간으로 만듦
       world[shopkeeper.y][shopkeeper.x] = TILE_TYPES.EMPTY;


       let caveX = 20;
       let caveY = Math.floor(MAP_HEIGHT / 2);
       for (let i = 0; i < 5000; i++) {
           // Check boundaries before modifying the world
           if (caveY >= 0 && caveY < MAP_HEIGHT && caveX >= 0 && caveX < MAP_WIDTH) {
               world[caveY][caveX] = TILE_TYPES.EMPTY;
               if (caveY > 0) world[caveY - 1][caveX] = TILE_TYPES.EMPTY;
               if (caveY < MAP_HEIGHT - 1) world[caveY + 1][caveX] = TILE_TYPES.EMPTY;
               if (caveX > 0) world[caveY][caveX - 1] = TILE_TYPES.EMPTY;
               if (caveX < MAP_WIDTH - 1) world[caveY][caveX + 1] = TILE_TYPES.EMPTY;
           }


           const direction = Math.floor(Math.random() * 4);
           switch(direction) {
               case 0: if (caveX < MAP_WIDTH - 1) caveX++; break;
               // Changed from > 1 to > 0 to allow path to go to the edge
               case 1: if (caveX > 0) caveX--; break;
               case 2: if (caveY < MAP_HEIGHT - 1) caveY++; break;
               // Changed from > 1 to > 0 to allow path to go to the edge
               case 3: if (caveY > 0) caveY--; break;
           }
       }


       for (let y = 0; y < MAP_HEIGHT; y++) {
           for (let x = 0; x < MAP_WIDTH; x++) {
               if (world[y][x] === TILE_TYPES.EMPTY) {
                   const rand = Math.random();
                   // 광석 생성 확률을 대폭 증가시킴
                   if (rand < 0.001) {
                       world[y][x] = TILE_TYPES.RUBY;
                   } else if (rand < 0.003) {
                       world[y][x] = TILE_TYPES.SAPPHIRE;
                   } else if (rand < 0.008) {
                       world[y][x] = TILE_TYPES.EMERALD;
                   } else if (rand < 0.02) {
                       world[y][x] = TILE_TYPES.DIAMOND;
                   } else if (rand < 0.05) {
                       world[y][x] = TILE_TYPES.REDSTONE;
                   } else if (rand < 0.1) {
                       world[y][x] = TILE_TYPES.LAPIS;
                   } else if (rand < 0.2) {
                       world[y][x] = TILE_TYPES.GOLD;
                   } else if (rand < 0.5) {
                       world[y][x] = TILE_TYPES.IRON;
                   } else if (rand < 0.7) {
                       world[y][x] = TILE_TYPES.COAL;
                   }
               }
           }
       }
   }


   function draw() {
       ctx.clearRect(0, 0, canvas.width, canvas.height);


       const startX = Math.max(0, player.x - Math.floor(GRID_WIDTH / 2));
       const startY = Math.max(0, player.y - Math.floor(GRID_HEIGHT / 2));
       const endX = Math.min(MAP_WIDTH, startX + GRID_WIDTH);
       const endY = Math.min(MAP_HEIGHT, startY + GRID_HEIGHT);


       for (let y = startY; y < endY; y++) {
           for (let x = startX; x < endX; x++) {
               const tileType = world[y][x];
               const drawX = (x - startX) * TILE_SIZE;
               const drawY = (y - startY) * TILE_SIZE;


               if (tileType === TILE_TYPES.STONE) {
                   drawStoneTexture(drawX, drawY, TILE_SIZE);
               } else if (tileType > TILE_TYPES.STONE) {
                   drawOreTexture(drawX, drawY, TILE_SIZE, tileType);
               } else {
                   ctx.fillStyle = TILE_COLORS[tileType];
                   ctx.fillRect(drawX, drawY, TILE_SIZE, TILE_SIZE);
               }
           }
       }


       // 플레이어 그리기
       ctx.fillStyle = player.color;
       const playerDrawX = (player.x - startX) * TILE_SIZE;
       const playerDrawY = (player.y - startY) * TILE_SIZE;
       ctx.fillRect(playerDrawX, playerDrawY, TILE_SIZE, TILE_SIZE);


       // 상점 NPC 그리기
       ctx.fillStyle = shopkeeper.color;
       const shopkeeperDrawX = (shopkeeper.x - startX) * TILE_SIZE;
       const shopkeeperDrawY = (shopkeeper.y - startY) * TILE_SIZE;
       ctx.fillRect(shopkeeperDrawX, shopkeeperDrawY, TILE_SIZE, TILE_SIZE);


       drawMiningTarget();
   }


   function drawMiningTarget() {
       let targetX, targetY;
       switch (player.direction) {
           case 'up': targetX = player.x; targetY = player.y -



