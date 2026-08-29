
(function() {
    // Helper functions for System Info
    function getBrowserName() {
        const ua = navigator.userAgent;
        if (ua.includes("Firefox")) return "Firefox";
        if (ua.includes("Opera") || ua.includes("OPR")) return "Opera";
        if (ua.includes("Edg")) return "Edge";
        if (ua.includes("Chrome")) return "Chrome";
        if (ua.includes("Safari")) return "Safari";
        return "Unknown";
    }

    function getOS() {
        const ua = navigator.userAgent;
        if (ua.includes("Win")) return "Windows";
        if (ua.includes("Mac")) return "macOS";
        if (ua.includes("Linux")) return "Linux";
        if (ua.includes("Android")) return "Android";
        if (ua.includes("like Mac")) return "iOS";
        return "Unknown";
    }

    // 1. UI CREATION (Floating Panel)
    const panel = document.createElement('div');
    panel.id = 'thiago-mouse-tracker';
    panel.style.cssText = `
        position: fixed; top: 20px; right: 20px; width: 480px; height: 480px;
        background: rgba(15, 45, 15, 0.75); backdrop-filter: blur(8px);
        border: 2px solid #00ff00; z-index: 999999;
        display: flex; flex-direction: column; resize: both; overflow: hidden;
        box-shadow: 0 12px 30px rgba(0, 255, 0, 0.2); font-family: 'Segoe UI', system-ui, sans-serif;
        border-radius: 12px; box-sizing: border-box; color: #00ff00;
    `;

    // Header
    const header = document.createElement('div');
    header.style.cssText = `
        background: rgba(0, 30, 0, 0.8); padding: 12px 14px; cursor: move;
        user-select: none; border-top-left-radius: 10px; border-top-right-radius: 10px;
        border-bottom: 1px solid #00ff00; display: flex; 
        justify-content: space-between; align-items: center;
    `;
    
    const title = document.createElement('span');
    title.textContent = 'Dragon Radar Tracker - Thiago Ventura';
    title.style.cssText = 'color: #00ff00; font-weight: 600; font-size: 14px; text-shadow: 0 0 5px #00ff00;';
    
    const closeBtn = document.createElement('span');
    closeBtn.textContent = '×'; // Substituído innerHTML pelo caractere diretamente via textContent
    closeBtn.style.cssText = 'color: #00ff00; font-size: 24px; font-weight: bold; cursor: pointer; line-height: 1; transition: text-shadow 0.2s; padding: 0 5px;';
    closeBtn.onmouseenter = () => closeBtn.style.textShadow = '0 0 10px #00ff00';
    closeBtn.onmouseleave = () => closeBtn.style.textShadow = 'none';
    
    header.appendChild(title);
    header.appendChild(closeBtn);
    panel.appendChild(header);

    // Controls Section
    const controls = document.createElement('div');
    controls.style.cssText = 'padding: 15px; display: flex; flex-direction: column; gap: 12px; background: rgba(0,0,0,0.4); border-bottom: 1px solid rgba(0,255,0,0.3);';
    
    const btnContainer = document.createElement('div');
    btnContainer.style.cssText = 'display: flex; gap: 10px;';

    const btnStart = document.createElement('button');
    btnStart.textContent = '▶ Start Scan';
    btnStart.style.cssText = 'flex: 1; padding: 10px; background: #006600; color: #00ff00; border: 1px solid #00ff00; border-radius: 6px; cursor: pointer; font-weight: bold; transition: background 0.2s;';
    
    const btnStop = document.createElement('button');
    btnStop.textContent = '⏹ Stop Scan';
    btnStop.disabled = true;
    btnStop.style.cssText = 'flex: 1; padding: 10px; background: #330000; color: #ff3333; border: 1px solid #ff3333; border-radius: 6px; cursor: pointer; font-weight: bold; opacity: 0.5; transition: background 0.2s;';
    
    btnContainer.appendChild(btnStart);
    btnContainer.appendChild(btnStop);
    controls.appendChild(btnContainer);

    // System Info Panel (Construído via DOM em vez de innerHTML)
    const sysInfoContainer = document.createElement('div');
    sysInfoContainer.style.cssText = 'display: grid; grid-template-columns: 1fr 1fr; gap: 6px; font-size: 11px; color: #88ff88; margin-top: 5px; padding-top: 10px; border-top: 1px dashed rgba(0,255,0,0.3);';
    
    function createInfoRow(label, value) {
        const div = document.createElement('div');
        const strong = document.createElement('strong');
        strong.style.color = '#00ff00';
        strong.textContent = label + ': ';
        div.appendChild(strong);
        div.appendChild(document.createTextNode(value));
        return div;
    }

    sysInfoContainer.appendChild(createInfoRow('OS', getOS()));
    sysInfoContainer.appendChild(createInfoRow('Browser', getBrowserName()));
    sysInfoContainer.appendChild(createInfoRow('Lang', navigator.language));
    sysInfoContainer.appendChild(createInfoRow('Res', `${window.screen.width} x ${window.screen.height}`));
    
    controls.appendChild(sysInfoContainer);
    panel.appendChild(controls);

    // Canvas Area
    const canvasContainer = document.createElement('div');
    canvasContainer.style.cssText = 'flex-grow: 1; background: transparent; padding: 10px; overflow: hidden; display: flex; justify-content: center; align-items: center; position: relative;';
    
    const canvas = document.createElement('canvas');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    canvas.style.cssText = 'width: 100%; height: 100%; object-fit: contain; border: 1px solid rgba(0,255,0,0.3); background: transparent; border-radius: 50%;';
    
    canvasContainer.appendChild(canvas);
    panel.appendChild(canvasContainer);
    document.body.appendChild(panel);

    // 2. DRAGGABLE LOGIC
    let isDragging = false, startX, startY, initialLeft, initialTop;
    header.addEventListener('mousedown', (e) => {
        if (e.target === closeBtn) return;
        isDragging = true;
        startX = e.clientX; startY = e.clientY;
        initialLeft = panel.offsetLeft; initialTop = panel.offsetTop;
    });
    document.addEventListener('mousemove', (e) => {
        if (isDragging) {
            panel.style.left = `${initialLeft + (e.clientX - startX)}px`;
            panel.style.top = `${initialTop + (e.clientY - startY)}px`;
            panel.style.right = 'auto';
        }
    });
    document.addEventListener('mouseup', () => isDragging = false);

    // 3. MAPPING LOGIC (Dragon Radar Style)
    const ctx = canvas.getContext('2d');
    let isRecording = false;
    let trackingData = [];
    let startTime = 0;
    let lastEventTime = 0;
    let currentMouseX = window.innerWidth / 2;
    let currentMouseY = window.innerHeight / 2;

    const eventsToTrack = ['mousemove', 'mousedown', 'click', 'keydown', 'keyup', 'change'];

    closeBtn.addEventListener('click', () => {
        if (isRecording) eventsToTrack.forEach(evt => document.removeEventListener(evt, recordEvent, true));
        panel.remove();
    });

    function drawGrid() {
        const w = canvas.width;
        const h = canvas.height;
        const cx = w / 2;
        const cy = h / 2;
        const radius = Math.max(w, h);

        ctx.save();
        ctx.clearRect(0, 0, w, h);
        
        // Export Dark Green Background
        if (!isRecording && trackingData.length > 0 && panel.style.display === 'none') {
             ctx.fillStyle = '#051a05'; 
             ctx.fillRect(0, 0, w, h);
        }

        ctx.strokeStyle = 'rgba(0, 255, 0, 0.4)';
        ctx.lineWidth = 1.5;

        // Draw Radar Concentric Circles
        ctx.beginPath();
        for (let r = 50; r <= radius; r += 100) {
            ctx.arc(cx, cy, r, 0, 2 * Math.PI);
        }
        ctx.stroke();

        // Draw Radar Crosshairs
        ctx.beginPath();
        ctx.moveTo(cx, 0); ctx.lineTo(cx, h);
        ctx.moveTo(0, cy); ctx.lineTo(w, cy);
        ctx.stroke();
        
        ctx.restore();
    }

    function setupMouseDrawing() {
        ctx.strokeStyle = 'rgba(0, 255, 0, 0.8)'; // Green tracker line
        ctx.lineWidth = 2;
        ctx.lineCap = 'round';
        ctx.lineJoin = 'round';
        ctx.beginPath();
    }

    drawGrid();

    function recordEvent(e) {
        if (!isRecording || panel.contains(e.target)) return;

        const currentTime = Date.now();
        const timeElapsed = currentTime - startTime;
        const latency = lastEventTime === 0 ? 0 : currentTime - lastEventTime;
        lastEventTime = currentTime;

        if (e.clientX !== undefined) currentMouseX = e.clientX;
        if (e.clientY !== undefined) currentMouseY = e.clientY;

        const x = currentMouseX;
        const y = currentMouseY;
        const type = e.type;

        let targetDetail = e.target.tagName ? e.target.tagName.toLowerCase() : 'unknown';
        if (e.target.id) targetDetail += `#${e.target.id}`;
        else if (e.target.className && typeof e.target.className === 'string') targetDetail += `.${e.target.className.split(' ')[0]}`;
        
        if (e.target.innerText && targetDetail.includes('button')) {
            targetDetail += ` ("${e.target.innerText.substring(0, 15).trim()}...")`;
        }
        if (e.key) targetDetail += ` [Key: ${e.key}]`;
        if (e.target.value && type === 'change') targetDetail += ` [Value changed]`;

        trackingData.push({ time: timeElapsed, latency, x, y, type, detail: targetDetail });

        // Visual Plotting
        if (type === 'mousemove') {
            ctx.lineTo(x, y);
            ctx.stroke();
        } else if (type === 'mousedown' || type === 'click') {
            const gradient = ctx.createRadialGradient(x - 3, y - 3, 2, x, y, 12);
            gradient.addColorStop(0, '#ffcc00');
            gradient.addColorStop(1, '#d35400');
            
            ctx.beginPath();
            ctx.arc(x, y, 12, 0, 2 * Math.PI);
            ctx.fillStyle = gradient;
            ctx.fill();
            ctx.strokeStyle = '#a04000';
            ctx.lineWidth = 2;
            ctx.stroke();
            
            ctx.beginPath();
            ctx.moveTo(x, y);
            setupMouseDrawing();
        } else if (['keydown', 'keyup', 'change'].includes(type)) {
            const randomColor = `hsl(${Math.floor(Math.random() * 360)}, 100%, 60%)`;
            ctx.beginPath();
            ctx.arc(x, y, 7, 0, 2 * Math.PI);
            ctx.fillStyle = randomColor;
            ctx.fill();
            ctx.strokeStyle = '#fff';
            ctx.lineWidth = 1;
            ctx.stroke();
            
            ctx.beginPath();
            ctx.moveTo(x, y);
            setupMouseDrawing();
        }
    }

    // 4. BUTTON ACTIONS
    btnStart.addEventListener('click', () => {
        isRecording = true;
        trackingData = [];
        startTime = Date.now();
        lastEventTime = 0;
        
        btnStart.disabled = true; btnStart.style.opacity = '0.5';
        btnStop.disabled = false; btnStop.style.opacity = '1';

        drawGrid();
        setupMouseDrawing();
        eventsToTrack.forEach(evt => document.addEventListener(evt, recordEvent, true));
    });

    btnStop.addEventListener('click', () => {
        isRecording = false;
        
        btnStart.disabled = false; btnStart.style.opacity = '1';
        btnStop.disabled = true; btnStop.style.opacity = '0.5';

        eventsToTrack.forEach(evt => document.removeEventListener(evt, recordEvent, true));

        panel.style.display = 'none';
        setTimeout(() => {
            downloadCSV();
            downloadJPG();
            panel.style.display = 'flex';
            drawGrid();
        }, 150);
    });

    // 5. EXPORTS
    function downloadCSV() {
        let csvContent = "Time Elapsed (ms),Latency (ms),X,Y,Action,Target Details\n";
        trackingData.forEach(row => {
            const safeDetail = `"${row.detail.replace(/"/g, '""')}"`;
            csvContent += `${row.time},${row.latency},${row.x},${row.y},${row.type},${safeDetail}\n`;
        });
        
        const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
        const url = URL.createObjectURL(blob);
        const link = document.createElement("a");
        link.href = url;
        link.download = `dragon_radar_data_${Date.now()}.csv`;
        document.body.appendChild(link); link.click(); document.body.removeChild(link);
    }

    function downloadJPG() {
        const link = document.createElement('a');
        link.download = `dragon_radar_map_${Date.now()}.jpg`;
        link.href = canvas.toDataURL('image/jpeg', 0.95);
        link.click();
    }
})();
