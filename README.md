<html><head><base href="https://voice-timer.app/">
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Voice-Controlled Multi-Timer</title>
<style>
:root {
  --time-color: rgba(255, 118, 117, 0.5);
}
body {
  background-color: #000000; /* Coal black */
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  min-height: 100vh;
}
.time-color-strip {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 0; /* Start with no height */
  z-index: -1;
  opacity: 0.3;
  transition: background-color 1s linear, height 1s linear;
  overflow: hidden;
}
.star {
  position: absolute;
  background-color: #ffffff;
  border-radius: 50%;
  opacity: 0;
  transition: opacity 0.5s ease-in-out;
}
.container {
  background-color: #333333; /* Dark gray */
  background-image: 
    linear-gradient(45deg, #2b2b2b 25%, transparent 25%), 
    linear-gradient(-45deg, #2b2b2b 25%, transparent 25%), 
    linear-gradient(45deg, transparent 75%, #2b2b2b 75%), 
    linear-gradient(-45deg, transparent 75%, #2b2b2b 75%);
  background-size: 20px 20px;
  background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
  flex: 1;
  margin: 0 20px;
  text-align: center;
  padding: 40px; /* Increased from 30px */
  border-radius: 15px; /* Adjusted for rounded corners */
  box-shadow: 0 0 20px rgba(0,0,0,0.2);
  max-width: 800px; /* Increased from 600px */
  margin-top: 20px; /* Reduced from 240px to move it higher */
  position: relative;
  z-index: 1; /* Ensure it's above the color strip */
}
h1 {
  color: #ffd700; /* Golden color */
  text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
}
#startButton {
  font-size: 24px;
  padding: 15px 30px;
  background-color: #00b894;
  color: white;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s;
  margin: 10px;
}
#startButton:hover {
  background-color: #00a885;
}
#status { font-size: 18px; margin-bottom: 20px; color: #dfe6e9; }
#timerList { text-align: left; margin-top: 20px; }
.timer-item {
  background-color: rgba(255, 255, 255, 0.1);
  border: 2px solid #00b894;
  padding: 15px;
  margin-bottom: 15px;
  border-radius: 15px; /* Adjusted for rounded corners */
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: all 0.3s;
  cursor: pointer;
}
.timer-item.completed {
  background-color: rgba(0, 184, 148, 0.2);
  border-color: #00a885;
}
.timer-item .time { font-weight: bold; color: #ff7675; }
.timer-item .command { color: #74b9ff; }
.slider-container {
  margin-top: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.slider-container label {
  color: #dfe6e9;
  margin-right: 10px;
}
#eqSlider {
  width: 200px;
}
.donation-info {
  position: fixed;
  bottom: 10px;
  right: 10px;
  text-align: right;
  background-color: rgba(255,255,255,0.1);
  padding: 10px;
  border-radius: 15px; /* Adjusted for rounded corners */
  max-width: 300px;
  font-size: 14px;
  color: #dfe6e9;
}
.clock-container {
  position: absolute;
  top: 20px;
  right: 20px; /* Keep the analog clock on the right side */
  display: flex;
  flex-direction: column;
  align-items: flex-end; /* Changed from flex-start */
  z-index: 2;
}
.scales {
  position: absolute;
  top: 20px;
  left: 20px; /* Move scales to the left side */
  display: flex;
  height: calc(100vh - 40px); /* Full viewport height minus top/bottom margin */
  z-index: 2;
}
.scale {
  width: 30px;
  height: 100%; /* Changed to 100% */
  background-color: #222;
  margin-right: 20px; /* Increased from 10px to separate scales further */
  position: relative;
  border-radius: 15px; /* Adjusted for rounded corners */
}
.scale::after {
  content: '';
  position: absolute;
  top: 0; /* Change from bottom to top */
  left: 0;
  width: 100%;
  background-color: var(--time-color);
  transition: height 1s linear, background-color 1s linear;
  border-radius: 0 0 15px 15px;
}
.scale-mark {
  position: absolute;
  top: 0; /* Change from bottom to top */
  left: 0;
  height: 1px;
  background-color: #00b894; /* Same color as the Start button */
  width: 10px; /* Make all marks visible */
  border-radius: 0 5px 5px 0;
}
.scale-mark.numbered {
  background-color: #333; /* Slightly lighter for numbered marks */
  width: 15px; /* Make numbered marks slightly longer */
}
.seconds-fill {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  background-color: rgba(116, 185, 255, 0.5); /* Light blue, semi-transparent */
  transition: height 1s linear;
  border-radius: 0 0 15px 15px;
}
.hours::after {
  height: calc(var(--current-hour) / 24 * 100%);
}
.minutes::after {
  height: calc(var(--current-minute) / 60 * 100%);
}
.scale-text {
  position: absolute;
  left: 20px;
  transform: translateY(-50%);
  color: #00b894; /* Same color as the Start button */
}
.instructions {
  width: 200px;
  margin-left: 20px;
  color: #dfe6e9;
  font-size: 14px;
}
.instructions h3 {
  color: #ffd700;
  margin-bottom: 10px;
}
.instructions ul {
  padding-left: 20px;
  margin: 0;
}
.instructions li {
  margin-bottom: 10px;
}
.clock {
  width: 200px;
  height: 200px;
  position: relative; /* Changed from absolute */
  margin-bottom: 20px; /* Add some space between clock and scales */
}
.hand {
  position: absolute;
  bottom: 50%;
  left: 50%;
  transform-origin: 50% 100%;
  background-color: #dfe6e9;
}
.hour {
  width: 4px;
  height: 80px; /* Increased height */
  background-color: #ffd700; /* Golden color */
}
.minute {
  width: 3px;
  height: 100px; /* Increased height */
  background-color: #ffd700; /* Golden color */
}
.second {
  width: 2px;
  height: 110px; /* Increased height */
  background-color: #ff7675;
}
.center-dot {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: #dfe6e9;
  transform: translate(-50%, -50%);
}
</style>
</head>
<body>
  <div class="time-color-strip"></div>
  <div class="honeycomb"></div>
  <div class="clock-container">
    <div class="clock">
      <div class="hand hour"></div>
      <div class="hand minute"></div>
      <div class="hand second"></div>
      <div class="center-dot"></div>
    </div>
  </div>
  <div class="scales">
    <div class="scale hours" id="hourScale"></div>
    <div class="scale minutes" id="minuteScale">
      <div class="seconds-fill"></div>
    </div>
    <div class="instructions">
      <h3>Instructions</h3>
      <ul>
        <li>Press "Start" or Space key to begin voice input.</li>
        <li>Say any word or sound.</li>
        <li>Press "Start" or the space bar again.</li>
        <li>Speak the timer duration (e.g., "5 minutes").</li>
        <li>For alarms, say the time (e.g., "3:30").</li>
        <li>Alternative installation option:</li>
        <li>Click scales on the left hand side to set timers visually.</li>
        <li>Double tap for any length of time in minutes.</li>
        <li>When set for a certain time:</li>
        <li>A single tap on the watch then a double tap on the minutes.</li>
        <li>Long click a timer for 2 seconds to remove it.</li>
        <li>Double-click a completed timer to restart it.</li>
        <li>Adjust the slider for audio EQ.</li>
        <li>After a minute, a control beep.</li>
        <li>Remember that the performance of the timer depends on the stability of your Internet.</li>
      </ul>
    </div>
  </div>
  <div class="container">
    <h1 id="mainTitle">Snotty Timer with Voice Input</h1>
    <div id="status">Press the button or "Space" key to start</div>
    <button id="startButton">Start</button>
    <div class="slider-container">
      <label for="eqSlider">Bass</label>
      <input type="range" id="eqSlider" min="-10" max="10" value="0">
      <label for="eqSlider">Treble</label>
    </div>
    <div id="timerList"></div>
  </div>
  <div class="donation-info">
    <p>Your support, even a few cents, helps keep this app free. Thank you for being awesome! Peace and goodwill to all.</p>
    <p style="font-size: 12px; word-break: break-all;">ETH address: 0x896d8d26417DfeE7a49B75aa68ac40A23E7F5F2c</p>
    <img src="https://api.qrserver.com/v1/create-qr-code/?size=100x100&data=ethereum:0x896d8d26417DfeE7a49B75aa68ac40A23E7F5F2c" alt="QR Code">
    <button onclick="donateETH()">Donate with MetaMask</button>
    <p style="font-size: 12px;">No MetaMask? Use the QR code or copy the address above to donate from any ETH wallet.</p>
  </div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/RecordRTC/5.6.2/RecordRTC.min.js"></script>
<script>
const startButton = document.getElementById('startButton');
const statusDisplay = document.getElementById('status');
const timerList = document.getElementById('timerList');
const eqSlider = document.getElementById('eqSlider');

let isRecording = false;
let recorder;
let stream;
let timers = [];
let audioContext;
let gainNode;
let biquadFilter;

startButton.addEventListener('click', toggleRecording);
document.addEventListener('keydown', (e) => {
  if (e.code === 'Space') {
    e.preventDefault();
    toggleRecording();
  }
});

eqSlider.addEventListener('input', updateEQ);

function updateEQ() {
  if (biquadFilter) {
    const value = parseFloat(eqSlider.value);
    biquadFilter.type = value < 0 ? "lowshelf" : "highshelf";
    biquadFilter.frequency.value = value < 0 ? 200 : 2000;
    biquadFilter.gain.value = Math.abs(value) * 8; // Increased gain for more pronounced effect
  }
}

function toggleRecording() {
  if (isRecording) {
    stopRecording();
  } else {
    startRecording();
  }
}

function startRecording() {
  navigator.mediaDevices.getUserMedia({ audio: true })
    .then(audioStream => {
      stream = audioStream;
      recorder = new RecordRTC(stream, {
        type: 'audio',
        mimeType: 'audio/webm',
        sampleRate: 44100,
        desiredSampRate: 16000,
        recorderType: RecordRTC.StereoAudioRecorder,
        numberOfAudioChannels: 1
      });
      
      recorder.startRecording();
      isRecording = true;
      startButton.textContent = 'Stop';
      statusDisplay.textContent = 'Make any sound'; // Changed from 'Speak the time for the timer...'
    })
    .catch(err => {
      console.warn('Unable to access microphone:', err);
      statusDisplay.textContent = 'Unable to access microphone';
    });
}

const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
recognition.continuous = false;
recognition.interimResults = false;
recognition.maxAlternatives = 1;
recognition.lang = 'ru-RU, en-US';

recognition.onresult = (event) => {
  recognition.resultReceived = true;
  const speechResult = event.results[0][0].transcript.toLowerCase();
  console.log('Recognized speech:', speechResult);
  parseVoiceCommand(speechResult, recorder.getBlob());
};

recognition.onerror = (event) => {
  console.warn('Speech recognition issue:', event.error);
  statusDisplay.textContent = 'Speech recognition issue occurred';
};

recognition.onend = () => {
  console.log('Speech recognition ended');
  if (!recognition.resultReceived) {
    statusDisplay.textContent = 'No speech detected. Please try again.';
  }
};

function stopRecording() {
  if (!isRecording) return;
  
  recorder.stopRecording(() => {
    isRecording = false;
    startButton.textContent = 'Start';
    statusDisplay.textContent = 'Speak the time for the timer'; // Changed from 'Processing voice input...'
    
    stream.getTracks().forEach(track => track.stop());
    
    setTimeout(() => {
      recognition.start();
      
      setTimeout(() => {
        if (recognition.state === 'running') {
          recognition.stop();
        }
      }, 4000);
    }, 500);
  });
}

function parseVoiceCommand(command, audioBlob) {
  const numbers = command.match(/\d+/g);
  if (numbers && numbers.length > 0) {
    if (numbers.length >= 2) {
      const hours = parseInt(numbers[0]);
      const minutes = parseInt(numbers[1]);
      addAlarmTimer(hours, minutes, command, audioBlob);
    } else {
      const minutes = parseInt(numbers[0]);
      addCountdownTimer(minutes * 60, command, audioBlob);
    }
  } else {
    statusDisplay.textContent = 'Could not recognize the time';
  }
}

function addCountdownTimer(seconds, command, audioBlob) {
  const timer = {
    id: Date.now(),
    type: 'countdown',
    startTime: Date.now(),
    endTime: Date.now() + seconds * 1000,
    command: command,
    audioBlob: audioBlob,
    completed: false,
    reminded: false
  };
  timers.unshift(timer);
  updateTimerList();
  startTimer(timer);
}

function addAlarmTimer(hours, minutes, command, audioBlob) {
  const now = new Date();
  const alarmTime = new Date(now.getFullYear(), now.getMonth(), now.getDate(), hours, minutes);
  
  if (alarmTime <= now) {
    alarmTime.setDate(alarmTime.getDate() + 1);
  }
  
  const timer = {
    id: Date.now(),
    type: 'alarm',
    startTime: now.getTime(),
    endTime: alarmTime.getTime(),
    command: command,
    audioBlob: audioBlob,
    completed: false,
    reminded: false
  };
  timers.unshift(timer);
  updateTimerList();
  startTimer(timer);
}

const timerIntervals = {};

function startTimer(timer) {
  const updateInterval = setInterval(() => {
    const now = Date.now();
    if (now >= timer.endTime) {
      clearInterval(updateInterval);
      timer.completed = true;
      if (timer.audioBlob) {
        playAudio(timer.audioBlob);
      } else {
        playDefaultSound();
      }
      updateTimerList();
      
      setTimeout(() => {
        if (!timer.reminded && timers.some(t => t.id === timer.id)) {
          playReminderBeep();
          timer.reminded = true;
          updateTimerList();
        }
      }, 60000);
    } else {
      updateTimerList();
    }
  }, 1000);
  
  timerIntervals[timer.id] = updateInterval;
}

function updateTimerList() {
  requestAnimationFrame(() => {
    timerList.innerHTML = '';
    timers.sort((a, b) => b.id - a.id).forEach(timer => {
      const timerElement = document.createElement('div');
      timerElement.className = `timer-item ${timer.completed ? 'completed' : ''}`;
      
      const remainingTime = Math.max(0, Math.floor((timer.endTime - Date.now()) / 1000));
      const hours = Math.floor(remainingTime / 3600);
      const minutes = Math.floor((remainingTime % 3600) / 60);
      const seconds = remainingTime % 60;
      
      timerElement.innerHTML = `
        <span class="command">${timer.command}</span>
        <span class="time">${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}</span>
      `;
      
      let longPressTimer;
      timerElement.addEventListener('mousedown', () => {
        longPressTimer = setTimeout(() => {
          removeTimer(timer.id);
        }, 2000); // Changed to 2 seconds
      });
      
      timerElement.addEventListener('mouseup', () => {
        clearTimeout(longPressTimer);
      });
      
      timerElement.addEventListener('dblclick', () => {
        restartTimer(timer);
      });
      
      timerList.appendChild(timerElement);
    });
  });
}

function removeTimer(timerId) {
  timers = timers.filter(timer => timer.id !== timerId);
  updateTimerList();
  clearInterval(timerIntervals[timerId]);
  delete timerIntervals[timerId];
}

function restartTimer(timer) {
  const duration = timer.endTime - timer.startTime;
  timer.startTime = Date.now();
  timer.endTime = timer.startTime + duration;
  timer.completed = false;
  timer.reminded = false;
  startTimer(timer);
}

function playDefaultSound() {
  const audioContext = new (window.AudioContext || window.webkitAudioContext)();
  const oscillator = audioContext.createOscillator();
  const gainNode = audioContext.createGain();

  oscillator.type = 'sine';
  oscillator.frequency.setValueAtTime(2000, audioContext.currentTime);
  oscillator.connect(gainNode);
  gainNode.connect(audioContext.destination);

  gainNode.gain.setValueAtTime(0, audioContext.currentTime);
  gainNode.gain.linearRampToValueAtTime(1, audioContext.currentTime + 0.01);
  gainNode.gain.linearRampToValueAtTime(0, audioContext.currentTime + 1);

  oscillator.start(audioContext.currentTime);
  oscillator.stop(audioContext.currentTime + 1);
}

function playAudio(audioBlob) {
  if (!audioContext) {
    audioContext = new (window.AudioContext || window.webkitAudioContext)();
    gainNode = audioContext.createGain();
    biquadFilter = audioContext.createBiquadFilter();
    gainNode.connect(biquadFilter);
    biquadFilter.connect(audioContext.destination);
  }

  const reader = new FileReader();
  reader.onload = function(e) {
    audioContext.decodeAudioData(e.target.result, function(buffer) {
      const source = audioContext.createBufferSource();
      source.buffer = buffer;
      source.connect(gainNode);
      updateEQ();
      source.start(0);
    });
  };
  reader.readAsArrayBuffer(audioBlob);
}

function playReminderBeep() {
  if (!audioContext) {
    audioContext = new (window.AudioContext || window.webkitAudioContext)();
  }
  
  const oscillator = audioContext.createOscillator();
  const gainNode = audioContext.createGain();
  
  oscillator.connect(gainNode);
  gainNode.connect(audioContext.destination);
  
  oscillator.type = 'sine';
  oscillator.frequency.setValueAtTime(440, audioContext.currentTime);
  
  gainNode.gain.setValueAtTime(0, audioContext.currentTime);
  gainNode.gain.linearRampToValueAtTime(1, audioContext.currentTime + 0.01);
  gainNode.gain.linearRampToValueAtTime(0, audioContext.currentTime + 0.5);
  
  oscillator.start(audioContext.currentTime);
  oscillator.stop(audioContext.currentTime + 0.5);
}

function updateClock() {
  const now = new Date();
  const hours = now.getHours() % 12;
  const minutes = now.getMinutes();
  const seconds = now.getSeconds();

  document.querySelector('.hour').style.transform = `rotate(${(hours + minutes / 60) * 30}deg)`;
  document.querySelector('.minute').style.transform = `rotate(${(minutes + seconds / 60) * 6}deg)`;
  document.querySelector('.second').style.transform = `rotate(${seconds * 6}deg)`;
}

const updateInterval = setInterval(() => {
  updateClock();
  updateTimerList();
}, 1000);

updateClock(); // Initial call to set the clock immediately

function updateScaleColor() {
  const now = new Date();
  const hour = now.getHours();
  const minute = now.getMinutes();
  
  const timeOfDay = (hour * 60 + minute) / (24 * 60);
  
  let hue, saturation, lightness;
  
  if (timeOfDay < 0.25) { // Night to sunrise (0:00 - 6:00)
    hue = 240 + (timeOfDay / 0.25) * 20; // 240 (blue) to 260 (purple)
    saturation = 100 - (timeOfDay / 0.25) * 40; // 100% to 60%
    lightness = 20 + (timeOfDay / 0.25) * 30; // 20% to 50%
  } else if (timeOfDay < 0.5) { // Sunrise to midday (6:00 - 12:00)
    hue = 40 - ((timeOfDay - 0.25) / 0.25) * 40; // 40 (orange) to 0 (red)
    saturation = 100;
    lightness = 50 + ((timeOfDay - 0.25) / 0.25) * 30; // 50% to 80%
  } else if (timeOfDay < 0.75) { // Midday to sunset (12:00 - 18:00)
    hue = 200 * ((timeOfDay - 0.5) / 0.25); // 0 (red) to 200 (light blue)
    saturation = 100 - ((timeOfDay - 0.5) / 0.25) * 60; // 100% to 40%
    lightness = 80 - ((timeOfDay - 0.5) / 0.25) * 30; // 80% to 50%
  } else { // Sunset to night (18:00 - 24:00)
    hue = 200 + ((timeOfDay - 0.75) / 0.25) * 40; // 200 (light blue) to 240 (blue)
    saturation = 40 + ((timeOfDay - 0.75) / 0.25) * 60; // 40% to 100%
    lightness = 50 - ((timeOfDay - 0.75) / 0.25) * 30; // 50% to 20%
  }
  
  const color = `hsla(${Math.round(hue)}, ${Math.round(saturation)}%, ${Math.round(lightness)}%, 0.5)`;
  
  document.documentElement.style.setProperty('--time-color', color);
  
  const strip = document.querySelector('.time-color-strip');
  strip.style.backgroundColor = color;
  strip.style.height = `${(timeOfDay * 100).toFixed(2)}%`;
  
  updateStars(timeOfDay);
}

function createStars() {
  const strip = document.querySelector('.time-color-strip');
  for (let i = 0; i < 50; i++) {
    const star = document.createElement('div');
    star.className = 'star';
    star.style.left = `${Math.random() * 100}%`;
    star.style.top = `${Math.random() * 100}%`;
    star.style.width = `${Math.random() * 2 + 1}px`;
    star.style.height = star.style.width;
    strip.appendChild(star);
  }
}

function updateStars(timeOfDay) {
  const stars = document.querySelectorAll('.star');
  const visibility = timeOfDay < 0.25 || timeOfDay > 0.75 ? 1 : 
                     timeOfDay < 0.3 || timeOfDay > 0.7 ? 0.5 : 0;
  
  stars.forEach(star => {
    star.style.opacity = Math.random() * visibility;
  });
}

createStars();

setInterval(() => {
  updateScaleColor();
}, 1000);

// Initial calls
updateScaleColor();

function updateScales() {
  const now = new Date();
  const hours = now.getHours();
  const minutes = now.getMinutes();
  const seconds = now.getSeconds();

  document.querySelector('.hours').style.setProperty('--current-hour', hours);
  document.querySelector('.minutes').style.setProperty('--current-minute', minutes);
  document.querySelector('.seconds-fill').style.height = `${(seconds / 60) * 100}%`;
}

// Make sure to call updateScales() every second
setInterval(updateScales, 1000);
updateScales(); // Initial call

function createScales() {
  const hourScale = document.getElementById('hourScale');
  const minuteScale = document.getElementById('minuteScale');

  for (let i = 0; i <= 24; i++) {
    const mark = document.createElement('div');
    mark.className = 'scale-mark numbered';
    mark.style.top = `${(i / 24) * 100}%`; // Changed from bottom to top
    const text = document.createElement('span');
    text.className = 'scale-text';
    text.textContent = i;
    mark.appendChild(text);
    hourScale.appendChild(mark);
  }

  for (let i = 0; i <= 60; i++) {
    const mark = document.createElement('div');
    mark.className = 'scale-mark';
    mark.style.top = `${(i / 60) * 100}%`; // Changed from bottom to top
    if (i % 5 === 0) {
      mark.classList.add('numbered');
      const text = document.createElement('span');
      text.className = 'scale-text';
      if (i % 10 === 0) {
        text.classList.add('ten');
      }
      text.textContent = i;
      mark.appendChild(text);
    }
    minuteScale.appendChild(mark);
  }

  hourScale.addEventListener('click', handleHourClick);
  minuteScale.addEventListener('dblclick', handleMinuteDoubleClick);
}

let selectedHour = null;

function handleHourClick(event) {
  const rect = event.target.getBoundingClientRect();
  const y = event.clientY - rect.top;
  selectedHour = Math.floor((y / rect.height) * 24);
}

function handleMinuteDoubleClick(event) {
  const rect = event.target.getBoundingClientRect();
  const y = event.clientY - rect.top;
  const minutes = Math.round((y / rect.height) * 60); // Changed to Math.round for more accurate selection
  
  if (selectedHour !== null) {
    addAlarmTimer(selectedHour, minutes, `Timer set for ${selectedHour}:${minutes.toString().padStart(2, '0')}`, null);
    selectedHour = null;
  } else {
    addCountdownTimer(minutes * 60, `Timer set for ${minutes} minutes`, null);
  }
}

createScales();
setInterval(updateScales, 1000);
updateScales(); // Initial call

function donateETH() {
  const address = "0x896d8d26417DfeE7a49B75aa68ac40A23E7F5F2c";
  const url = `https://metamask.app.link/send/eth?address=${address}`;
  window.open(url, '_blank');
}
</script>
</body>
</html>
