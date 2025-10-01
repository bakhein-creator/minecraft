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
            font-size: 0.9rem;
        }
        .chat-message-user {
            font-size: 0.8rem;
            color: #bbb;
        }
        .chat-message-npc {
            background-color: #333; /* Darker background for NPC */
            color: #d8b4fe; /* Purple text for NPC */
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
            transition: background-color 0.2s;
        }
        .chat-send-btn:hover {
            background-color: #2980b9;
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
            border-radius: 8px; /* Match canvas border */
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
        .text-purple-300 { color: #d8b4fe; } /* Custom Tailwind class for NPC user ID */
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
            <p class="mt-1 text-cyan-400"><strong>💰 광물 판매:</strong> 채팅창에 '판매: 다이아몬드' 입력</p>
        </div>
        <div class="message-box" id="messageBox"></div>
        <div class="inventory-box">
            인벤토리: <span id="inventory"></span>
        </div>
        <div id="gameOverOverlay" class="game-over-overlay" style="display: none;">
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
            <input type="text" id="chatInput" class="chat-input" placeholder="메시지 입력..." autocomplete="off">
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
    
    // ====================================================================================
    // Firebase 및 Gemini API 설정 (Canvas 환경 변수 사용)
    // ====================================================================================
    // Global variables provided by the environment
    const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

    // Gemini API settings
    const apiKey = ""; // Canvas environment injects this
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    const systemPrompt = "당신은 '올드 맨 스티브'라는 이름의 친절하고 현명하며 약간 괴짜 같은 광산 세계의 상인입니다. 당신은 가뭄을 해결하기 위해 물을 판매합니다. 당신의 답변은 짧고 격려하며, 채굴, 게임 세계, 그리고 가뭄과 관련되어야 합니다. 당신이 AI나 언어 모델이라는 것을 언급하지 마십시오.";
    // ====================================================================================

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
    
    // Function to display chat messages in the UI
    function displayMessage(userId, text, isNpc = false) {
        const messageElement = document.createElement('div');
        messageElement.className = 'chat-message';
        
        let formattedUserId = userId;
        let userIdClass = 'text-gray-400';

        if (isNpc) {
            formattedUserId = '상인 (NPC)';
            messageElement.classList.add('chat-message-npc');
            userIdClass = 'text-purple-300'; // NPC color
        } else if (userId === currentUserId) {
            formattedUserId = '나 (' + userId.slice(0, 4) + '...)';
            messageElement.classList.add('bg-blue-600', 'bg-opacity-20');
        } else {
            formattedUserId = '플레이어 (' + userId.slice(0, 4) + '...)';
            messageElement.classList.add('bg-gray-600', 'bg-opacity-20');
        }

        // '상인:' 접두사는 제거하고 표시
        const displayText = text.startsWith("상인:") ? text.substring(3).trim() : text;

        messageElement.innerHTML = `<span class="${userIdClass} font-bold mr-2">[${formattedUserId}]</span> ${displayText}`;
        chatMessagesContainer.appendChild(messageElement);
        chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;
    }

    // Function to set up the real-time chat listener
    const setupChatListener = () => {
        if (!db) return;
        // timestamp 기준으로 정렬하여 쿼리
        const q = query(collection(db, chatCollectionPath), orderBy("timestamp", "asc"));

        onSnapshot(q, (snapshot) => {
            snapshot.docChanges().forEach((change) => {
                if (change.type === "added") {
                    const data = change.doc.data();
                    const userId = data.userId;
                    const text = data.text;
                    const isNpc = userId === '상인';
                    
                    // '판매:' 명령어는 게임 점수 처리용이므로 채팅창에 노출하지 않음
                    if (text.toLowerCase().startsWith("판매:")) return; 
                    
                    displayMessage(userId, text, isNpc);
                }
            });
        }, (error) => {
            console.error("채팅 리스너 오류:", error);
        });
    };

    // Authenticate and set up chat listener
    onAuthStateChanged(auth, async (user) => {
        if (user) {
            currentUserId = user.uid;
            userIdDisplay.textContent = currentUserId.slice(0, 8) + '...';
            setupChatListener();
        } else {
            // Check for custom auth token first
            if (initialAuthToken) {
                try {
                    await signInWithCustomToken(auth, initialAuthToken);
                } catch (error) {
                    console.error("Custom token sign in failed, attempting anonymous:", error);
                    await signInAnonymously(auth).catch((anonError) => {
                        console.error("Anonymous sign in failed:", anonError);
                    });
                }
            } else {
                // If no token, sign in anonymously
                await signInAnonymously(auth).catch((anonError) => {
                    console.error("Anonymous sign in failed:", anonError);
                });
            }
        }
    });

    // Function to get a response from the Gemini API
    const getShopkeeperResponse = async (userQuery) => {
        try {
            const payload = {
                contents: [{ parts: [{ text: userQuery }] }],
                config: {
                    systemInstruction: systemPrompt
                },
                tools: [{ googleSearch: {} }] // Enable Google Search Grounding
            };
            
            // Exponential backoff retry logic
            let response, result;
            const maxRetries = 3;
            for (let i = 0; i < maxRetries; i++) {
                const delay = Math.pow(2, i) * 1000;
                await new Promise(resolve => setTimeout(resolve, delay)); 

                response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (response.ok) {
                    result = await response.json();
                    break;
                }
                if (i === maxRetries - 1) throw new Error("API call failed after retries.");
            }

            const npcText = result.candidates?.[0]?.content?.parts?.[0]?.text || "죄송합니다, 지금은 답변해드릴 수 없습니다.";
            
            // Firestore에 NPC 응답 메시지 추가 (리스너가 채팅창 업데이트)
            addDoc(collection(db, chatCollectionPath), {
                userId: '상인', 
                text: npcText,
                timestamp: serverTimestamp()
            });

        } catch (error) {
            console.error("Gemini API call failed:", error);
            // API 호출 실패 시 메시지 처리
            addDoc(collection(db, chatCollectionPath), {
                userId: '상인', 
                text: "죄송합니다, 지금은 답변해드릴 수 없습니다. (API 연결 실패)",
                timestamp: serverTimestamp()
            });
        }
    };

    // Function to add a new message to Firestore
    const sendMessage = async () => {
        const messageText = chatInput.value.trim();
        if (messageText === "" || !currentUserId) return;
        
        // 1. Check if the message is a 'SALE' command (Handled locally by processShopCommand)
        if (messageText.toLowerCase().startsWith("판매:")) {
            // 사용자 메시지를 먼저 채팅창에 추가 (판매 명령을 로그로 남김)
            await addDoc(collection(db, chatCollectionPath), {
                userId: currentUserId,
                text: messageText,
                timestamp: serverTimestamp()
            });
            processShopCommand(messageText);
        }
        
        // 2. Check if the message is for the NPC
        else if (messageText.startsWith("상인:")) {
            const userQuery = messageText.substring(3).trim();
            // 사용자 메시지를 먼저 채팅창에 추가
            await addDoc(collection(db, chatCollectionPath), {
                userId: currentUserId,
                text: messageText,
                timestamp: serverTimestamp()
            });
            await getShopkeeperResponse(userQuery);
        } else {
            // 3. Regular chat message to Firestore
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
            x: 5,
            y: 5,
            color: '#3498db',
            direction: 'right',
            inventory: {}
        };
        shopkeeper = {
            x: 10,
            y: 5,
            color: '#2ecc71'
        };
        scoreElement.textContent = score;
        gameOverOverlay.style.display = 'none';
        generateCaveMap();
        draw();
        updateInventory();
        showMessage("게임이 재시작되었습니다! 목표: 5000점 이상 달성하여 가뭄을 해결하세요!", 5000, 'text-green-400');
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

        if (!oreColor) return; // 광물 유형이 없으면 종료

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


        // 입구 및 초기 빈 공간 생성
        for (let y = 0; y < 10; y++) {
            for (let x = 0; x < 20; x++) {
                world[y][x] = TILE_TYPES.EMPTY;
            }
        }
        // 상점 위치의 블록을 빈 공간으로 만듦
        world[shopkeeper.y][shopkeeper.x] = TILE_TYPES.EMPTY;


        // 동굴 통로 생성 (랜덤 워크)
        let caveX = 20;
        let caveY = Math.floor(MAP_HEIGHT / 2);
        for (let i = 0; i < 5000; i++) {
            if (caveY >= 0 && caveY < MAP_HEIGHT && caveX >= 0 && caveX < MAP_WIDTH) {
                world[caveY][caveX] = TILE_TYPES.EMPTY;
                // 주변 타일도 빈 공간으로 만듦 (동굴 크기 확장)
                if (caveY > 0) world[caveY - 1][caveX] = TILE_TYPES.EMPTY;
                if (caveY < MAP_HEIGHT - 1) world[caveY + 1][caveX] = TILE_TYPES.EMPTY;
                if (caveX > 0) world[caveY][caveX - 1] = TILE_TYPES.EMPTY;
                if (caveX < MAP_WIDTH - 1) world[caveY][caveX + 1] = TILE_TYPES.EMPTY;
            }


            const direction = Math.floor(Math.random() * 4);
            switch(direction) {
                case 0: if (caveX < MAP_WIDTH - 1) caveX++; break;
                case 1: if (caveX > 0) caveX--; break;
                case 2: if (caveY < MAP_HEIGHT - 1) caveY++; break;
                case 3: if (caveY > 0) caveY--; break;
            }
        }


        // 광석 배치
        for (let y = 0; y < MAP_HEIGHT; y++) {
            for (let x = 0; x < MAP_WIDTH; x++) {
                if (world[y][x] === TILE_TYPES.STONE) {
                    const rand = Math.random();
                    // 깊이에 따른 광물 분포 (선택 사항: 현재는 전체 맵에 동일 분포)
                    let oreType = TILE_TYPES.STONE;
                    if (y > 10) { // 지하 10칸 아래부터 광물 생성
                        if (rand < 0.001) oreType = TILE_TYPES.RUBY;
                        else if (rand < 0.003) oreType = TILE_TYPES.SAPPHIRE;
                        else if (rand < 0.008) oreType = TILE_TYPES.EMERALD;
                        else if (rand < 0.02) oreType = TILE_TYPES.DIAMOND;
                        else if (rand < 0.05) oreType = TILE_TYPES.REDSTONE;
                        else if (rand < 0.1) oreType = TILE_TYPES.LAPIS;
                        else if (rand < 0.2) oreType = TILE_TYPES.GOLD;
                        else if (rand < 0.5) oreType = TILE_TYPES.IRON;
                        else if (rand < 0.7) oreType = TILE_TYPES.COAL;
                    }
                    if (oreType !== TILE_TYPES.STONE) {
                        world[y][x] = oreType;
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
                    // TILE_TYPES.EMPTY
                    ctx.fillStyle = TILE_COLORS[tileType];
                    ctx.fillRect(drawX, drawY, TILE_SIZE, TILE_SIZE);
                }
            }
        }


        // 상점 NPC 그리기 (먼저 그려 플레이어에게 가려지지 않게 함)
        const shopkeeperDrawX = (shopkeeper.x - startX) * TILE_SIZE;
        const shopkeeperDrawY = (shopkeeper.y - startY) * TILE_SIZE;
        if (shopkeeperDrawX >= 0 && shopkeeperDrawX < canvas.width && shopkeeperDrawY >= 0 && shopkeeperDrawY < canvas.height) {
            ctx.fillStyle = shopkeeper.color;
            ctx.fillRect(shopkeeperDrawX, shopkeeperDrawY, TILE_SIZE, TILE_SIZE);
        }


        // 플레이어 그리기
        ctx.fillStyle = player.color;
        const playerDrawX = (player.x - startX) * TILE_SIZE;
        const playerDrawY = (player.y - startY) * TILE_SIZE;
        ctx.fillRect(playerDrawX, playerDrawY, TILE_SIZE, TILE_SIZE);


        drawMiningTarget();
    }


    function drawMiningTarget() {
        let targetX, targetY;
        switch (player.direction) {
            case 'up': targetX = player.x; targetY = player.y - 1; break;
            case 'down': targetX = player.x; targetY = player.y + 1; break;
            case 'left': targetX = player.x - 1; targetY = player.y; break;
            case 'right': targetX = player.x + 1; targetY = player.y; break;
            default: return;
        }


        // 맵 경계 확인
        if (targetX < 0 || targetX >= MAP_WIDTH || targetY < 0 || targetY >= MAP_HEIGHT) return;


        const tileType = world[targetY][targetX];
        if (tileType === TILE_TYPES.EMPTY) return; // 빈 공간은 대상이 아님


        const startX = Math.max(0, player.x - Math.floor(GRID_WIDTH / 2));
        const startY = Math.max(0, player.y - Math.floor(GRID_HEIGHT / 2));
        const drawX = (targetX - startX) * TILE_SIZE;
        const drawY = (targetY - startY) * TILE_SIZE;


        // 채굴 대상 테두리 그리기
        ctx.strokeStyle = '#e74c3c'; // 빨간색 테두리
        ctx.lineWidth = 3;
        ctx.strokeRect(drawX + 1, drawY + 1, TILE_SIZE - 2, TILE_SIZE - 2);
    }


    function movePlayer(dx, dy) {
        if (isGameOver) return;


        const newX = player.x + dx;
        const newY = player.y + dy;


        // 새로운 방향 설정
        if (dx === 1) player.direction = 'right';
        else if (dx === -1) player.direction = 'left';
        else if (dy === 1) player.direction = 'down';
        else if (dy === -1) player.direction = 'up';


        // 맵 경계 및 이동 가능 확인
        if (newX >= 0 && newX < MAP_WIDTH && newY >= 0 && newY < MAP_HEIGHT) {
            if (world[newY][newX] === TILE_TYPES.EMPTY || (newX === shopkeeper.x && newY === shopkeeper.y)) {
                player.x = newX;
                player.y = newY;
                draw();
                // 상인 근처 도착 메시지
                if (Math.abs(player.x - shopkeeper.x) <= 1 && Math.abs(player.y - shopkeeper.y) <= 1 && (player.x !== shopkeeper.x || player.y !== shopkeeper.y)) {
                    showMessage("상인 '올드 맨 스티브' 근처에 있습니다. 스페이스바를 눌러 상점을 이용하세요.", 3000, 'text-yellow-400');
                } else {
                    showMessage(""); // 메시지 지우기
                }
            } else {
                showMessage("돌이 길을 막고 있습니다. 스페이스바를 눌러 채굴하세요!", 2000, 'text-red-400');
            }
        }
    }


    function mine() {
        if (isGameOver) return;


        let targetX, targetY;
        switch (player.direction) {
            case 'up': targetX = player.x; targetY = player.y - 1; break;
            case 'down': targetX = player.x; targetY = player.y + 1; break;
            case 'left': targetX = player.x - 1; targetY = player.y; break;
            case 'right': targetX = player.x + 1; targetY = player.y; break;
            default: return;
        }


        if (targetX < 0 || targetX >= MAP_WIDTH || targetY < 0 || targetY >= MAP_HEIGHT) return;


        const minedTileType = world[targetY][targetX];


        if (minedTileType === TILE_TYPES.STONE || minedTileType > TILE_TYPES.STONE) {
            // 광물 획득
            const tileName = Object.keys(TILE_TYPES).find(key => TILE_TYPES[key] === minedTileType);
            
            if (minedTileType !== TILE_TYPES.STONE) {
                player.inventory[tileName] = (player.inventory[tileName] || 0) + 1;
                showMessage(`${tileName.substring(0, 1) + tileName.substring(1).toLowerCase()}을(를) 채굴했습니다!`, 1500, 'text-green-400');
            } else {
                showMessage("돌을 채굴했습니다.", 1500, 'text-gray-400');
            }


            // 타일을 빈 공간으로 변경
            world[targetY][targetX] = TILE_TYPES.EMPTY;
            draw();
            updateInventory();


            // 승리 조건은 판매 후 점수 확인으로 대체
        } else if (minedTileType === TILE_TYPES.EMPTY) {
            showMessage("빈 공간입니다. 이동하세요.", 1500);
        }
    }


    function interact() {
        if (isGameOver) return;


        // 상인과의 상호작용
        if (Math.abs(player.x - shopkeeper.x) <= 1 && Math.abs(player.y - shopkeeper.y) <= 1) {
            openShop();
        } else {
            mine();
        }
    }


    function openShop() {
        showMessage("✨ 올드 맨 스티브: '광물을 팔아 점수를 모아 물을 사세요! '판매: [광물 이름]'이라고 채팅하세요.'", 5000, 'text-cyan-300');
    }


    function processShopCommand(command) {
        const parts = command.toLowerCase().split(':').map(p => p.trim());
        if (parts.length !== 2 || parts[0] !== '판매') {
            return;
        }


        const rawItemName = parts[1].toUpperCase();
        const tileTypeIndex = TILE_TYPES[rawItemName];
        const tileValue = TILE_SCORES[tileTypeIndex];
        const quantity = player.inventory[rawItemName] || 0;
        const formattedItemName = rawItemName.substring(0, 1) + rawItemName.substring(1).toLowerCase();
        
        // 상인 NPC의 응답 메시지를 저장할 변수
        let npcResponseText = '';


        if (!tileTypeIndex || tileValue === undefined || rawItemName === 'STONE' || rawItemName === 'EMPTY') {
             npcResponseText = `"${formattedItemName}"은(는) 제가 사지 않는 물건이군요. 다른 광물을 가져오세요.`;
        } else if (quantity > 0) {
            const totalValue = quantity * tileValue;
            score += totalValue;
            player.inventory[rawItemName] = 0; // 전부 판매
            scoreElement.textContent = score;
            updateInventory();


            npcResponseText = `"${formattedItemName} ${quantity}개"를 ${totalValue}점에 판매했습니다. 가뭄이 조금씩 해소되고 있습니다! 현재 점수: ${score}점.`;
            // 승리 조건 확인 (5000점)
            if (score >= 5000) {
                gameOver(true);
            }
        } else {
            npcResponseText = `"${formattedItemName}"이(가) 인벤토리에 없습니다. 채굴하세요!`;
        }
        
        // 상인 메시지를 Firestore에 추가
        addDoc(collection(db, chatCollectionPath), {
            userId: '상인',
            text: npcResponseText,
            timestamp: serverTimestamp()
        });
    }


    function showMessage(text, duration = 3000, colorClass = 'text-white') {
        messageBox.textContent = text;
        messageBox.className = `message-box ${colorClass}`;
        if (duration > 0) {
            clearTimeout(messageBox.timer);
            messageBox.timer = setTimeout(() => {
                messageBox.textContent = '';
                messageBox.className = 'message-box';
            }, duration);
        }
    }


    function updateInventory() {
        let inventoryText = '';
        let isEmpty = true;
        for (const item in player.inventory) {
            if (player.inventory[item] > 0) {
                // 첫 글자만 대문자로 변환하여 표시
                const formattedItem = item.substring(0, 1) + item.substring(1).toLowerCase();
                inventoryText += `${formattedItem}: ${player.inventory[item]}개, `;
                isEmpty = false;
            }
        }
        inventoryElement.textContent = isEmpty ? '비어 있음' : inventoryText.slice(0, -2);
    }


    function gameOver(win = false) {
        isGameOver = true;
        gameOverOverlay.style.display = 'flex';
        const textElement = gameOverOverlay.querySelector('.game-over-text');
        if (win) {
            textElement.textContent = `가뭄 해결! 최종 점수: ${score}`;
            textElement.style.color = '#2ecc71';
        } else {
            textElement.textContent = `게임 오버! 최종 점수: ${score}`;
            textElement.style.color = '#e74c3c';
        }
    }


    // 이벤트 핸들러


    document.addEventListener('keydown', (e) => {
        if (isGameOver) return;
        switch (e.key) {
            case 'ArrowUp':
            case 'w':
                e.preventDefault();
                movePlayer(0, -1);
                break;
            case 'ArrowDown':
            case 's':
                e.preventDefault();
                movePlayer(0, 1);
                break;
            case 'ArrowLeft':
            case 'a':
                e.preventDefault();
                movePlayer(-1, 0);
                break;
            case 'ArrowRight':
            case 'd':
                e.preventDefault();
                movePlayer(1, 0);
                break;
            case ' ': // Spacebar
                e.preventDefault();
                interact();
                break;
        }
    });


    // 마우스 드래그를 이용한 화면 이동 (미니맵 역할)
    canvas.addEventListener('mousedown', (e) => {
        if (e.button === 2) { // 오른쪽 버튼
            e.preventDefault();
            isDragging = true;
            lastMouseX = e.clientX;
            lastMouseY = e.clientY;
        }
    });


    document.addEventListener('mousemove', (e) => {
        if (!isDragging || isGameOver) return;


        const dx = e.clientX - lastMouseX;
        const dy = e.clientY - lastMouseY;


        // 이동 속도 조절
        const speed = 0.05;


        // 플레이어 위치를 드래그 방향의 반대로 조정하여 월드를 움직이는 것처럼 보이게 함
        player.x = Math.max(0, Math.min(MAP_WIDTH - 1, player.x - Math.round(dx * speed)));
        player.y = Math.max(0, Math.min(MAP_HEIGHT - 1, player.y - Math.round(dy * speed)));


        lastMouseX = e.clientX;
        lastMouseY = e.clientY;


        draw();
    });


    document.addEventListener('mouseup', () => {
        isDragging = false;
    });


    // 캔버스 오른쪽 클릭 메뉴 방지
    canvas.addEventListener('contextmenu', (e) => {
        e.preventDefault();
    });


    // 게임 오버 버튼 이벤트
    playAgainBtn.addEventListener('click', resetGame);
    quitBtn.addEventListener('click', () => {
        showMessage("게임을 종료합니다. 다음 기회에!", 5000, 'text-red-400');
        // document.querySelector('.main-container').style.display = 'none';
    });
    
    // 게임 초기화
    resetGame();
</script>
</body>
</html>
