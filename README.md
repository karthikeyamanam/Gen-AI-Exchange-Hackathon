# Gen-AI-Exchange-Hackathon
from flask import Flask, request, jsonify, render_template
from googletrans import Translator
from gtts import gTTS
from io import BytesIO
import base64

app = Flask(__name__)
translator = Translator()

languages = {
    "af": "Afrikaans",
    "sq": "Albanian",
    "am": "Amharic",
    "ar": "Arabic",
    "hy": "Armenian",
    "az": "Azerbaijani",
    "eu": "Basque",
    "be": "Belarusian",
    "bn": "Bengali",
    "bs": "Bosnian",
    "bg": "Bulgarian",
    "ca": "Catalan",
    "ceb": "Cebuano",
    "ny": "Chichewa",
    "zh-cn": "Chinese (Simplified)",
    "zh-tw": "Chinese (Traditional)",
    "co": "Corsican",
    "hr": "Croatian",
    "cs": "Czech",
    "da": "Danish",
    "nl": "Dutch",
    "en": "English",
    "eo": "Esperanto",
    "et": "Estonian",
    "tl": "Filipino",
    "fi": "Finnish",
    "fr": "French",
    "fy": "Frisian",
    "gl": "Galician",
    "ka": "Georgian",
    "de": "German",
    "el": "Greek",
    "gu": "Gujarati",
    "ht": "Haitian Creole",
    "ha": "Hausa",
    "haw": "Hawaiian",
    "iw": "Hebrew",
    "hi": "Hindi",
    "hmn": "Hmong",
    "hu": "Hungarian",
    "is": "Icelandic",
    "ig": "Igbo",
    "id": "Indonesian",
    "ga": "Irish",
    "it": "Italian",
    "ja": "Japanese",
    "jw": "Javanese",
    "kn": "Kannada",
    "kk": "Kazakh",
    "km": "Khmer",
    "ko": "Korean",
    "ku": "Kurdish (Kurmanji)",
    "ky": "Kyrgyz",
    "lo": "Lao",
    "la": "Latin",
    "lv": "Latvian",
    "lt": "Lithuanian",
    "lb": "Luxembourgish",
    "mk": "Macedonian",
    "mg": "Malagasy",
    "ms": "Malay",
    "ml": "Malayalam",
    "mt": "Maltese",
    "mi": "Maori",
    "mr": "Marathi",
    "mn": "Mongolian",
    "my": "Myanmar (Burmese)",
    "ne": "Nepali",
    "no": "Norwegian",
    "ps": "Pashto",
    "fa": "Persian",
    "pl": "Polish",
    "pt": "Portuguese",
    "pa": "Punjabi",
    "ro": "Romanian",
    "ru": "Russian",
    "sm": "Samoan",
    "gd": "Scots Gaelic",
    "sr": "Serbian",
    "st": "Sesotho",
    "sn": "Shona",
    "sd": "Sindhi",
    "si": "Sinhala",
    "sk": "Slovak",
    "sl": "Slovenian",
    "so": "Somali",
    "es": "Spanish",
    "su": "Sundanese",
    "sw": "Swahili",
    "sv": "Swedish",
    "tg": "Tajik",
    "ta": "Tamil",
    "te": "Telugu",
    "th": "Thai",
    "tr": "Turkish",
    "uk": "Ukrainian",
    "ur": "Urdu",
    "uz": "Uzbek",
    "vi": "Vietnamese",
    "cy": "Welsh",
    "xh": "Xhosa",
    "yi": "Yiddish",
    "yo": "Yoruba",
    "zu": "Zulu"
}


@app.route("/")
def home():
    return render_template("index.html")

@app.route("/languages")
def get_languages():
    return jsonify(languages)

@app.route("/translate", methods=["POST"])
def translate_text():
    data = request.json
    text = data["text"]
    src = data["src"]
    tgt = data["tgt"]

    # Translate text
    translated = translator.translate(text, src=src, dest=tgt).text

    # Generate TTS in memory
    tts = gTTS(translated, lang=tgt)
    mp3_fp = BytesIO()
    tts.write_to_fp(mp3_fp)
    mp3_fp.seek(0)

    # Convert to base64 string
    mp3_base64 = base64.b64encode(mp3_fp.read()).decode("utf-8")

    return jsonify({"translated": translated, "audio_base64": mp3_base64})
/////
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>🌍 Push-to-Talk Voice Translator</title>
  <style>
    * { box-sizing: border-box; margin:0; padding:0; }
    body {
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      color: #f0f0f0;
      text-align: center;
      padding: 40px 20px;
    }
    h1 {
      font-size: 2.8rem;
      margin-bottom: 30px;
      letter-spacing: 1px;
      color: #ffffff;
      text-shadow: 1px 1px 5px rgba(0,0,0,0.3);
    }
    .translator-box {
      background: rgba(255,255,255,0.05);
      padding: 30px 25px;
      border-radius: 20px;
      max-width: 700px;
      margin: auto;
      box-shadow: 0 10px 25px rgba(0,0,0,0.5);
      backdrop-filter: blur(10px);
    }
    #screen {
      background: rgba(255,255,255,0.1);
      padding: 18px 20px;
      border-radius: 12px;
      margin-bottom: 20px;
      min-height: 60px;
      font-size: 1.3rem;
      font-weight: 500;
      color: #ffffff;
      text-align: left;
      overflow-wrap: break-word;
      box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
    }
    .select-container {
      position: relative;
      display: inline-block;
      width: 45%;
      margin: 10px 2%;
    }
    .select-container input {
      width: 100%;
      padding: 10px;
      border-radius: 10px;
      border: none;
      outline: none;
      font-size: 1rem;
      background: #ffffff;
      color: #000;
      cursor: pointer;
    }
    .select-container select {
      width: 100%;
      padding: 10px;
      border-radius: 10px;
      border: none;
      outline: none;
      font-size: 1rem;
      margin-top: 5px;
      display: none; 
      max-height: 150px;
      overflow-y: auto;
    }
    button {
      padding: 12px 20px;
      margin: 10px 5px;
      border-radius: 12px;
      border: none;
      font-weight: 600;
      font-size: 1rem;
      cursor: pointer;
      transition: 0.3s;
    }
    .swap-btn { background: #4caf50; color: white; }
    .swap-btn:hover { background: #3e8e41; }
    audio { display:none; }
    @media (max-width:700px){ .select-container{width:90%; margin:8px 0;} }
  </style>
</head>
<body>
<h1>🌍 Push-to-Talk Voice Translator</h1>

<div class="translator-box">
  <div id="screen">🎤 Hold SPACE and speak to translate...</div>

  <div class="select-container">
    <input type="text" id="srcSearch" placeholder="Search source language..." 
           onclick="showSelect('src')" oninput="filterOptions('src')">
    <select id="src" size="5" onchange="updateInput('src')"></select>
  </div>

  <div class="select-container">
    <input type="text" id="tgtSearch" placeholder="Search target language..." 
           onclick="showSelect('tgt')" oninput="filterOptions('tgt')">
    <select id="tgt" size="5" onchange="updateInput('tgt')"></select>
  </div>

  <div>
    <button class="swap-btn" onclick="swap()">🔄 Swap Languages</button>
  </div>

  <audio id="audioPlayer" autoplay></audio>
</div>

<script>
let recognition;
let listening = false;
let screen = document.getElementById("screen");
let languages = {};  // full dictionary of languages

// Load all languages
async function loadLanguages(){
  let res = await fetch("/languages");
  languages = await res.json();
  populateSelect('src', languages);
  populateSelect('tgt', languages);

  document.getElementById('src').value = 'en';
  document.getElementById('tgt').value = 'te';
  updateInput('src');
  updateInput('tgt');
}

// Populate select options
function populateSelect(id, langs){
  let select = document.getElementById(id);
  select.innerHTML = '';
  for(let [code,name] of Object.entries(langs)){
    let option = document.createElement('option');
    option.value = code;
    option.textContent = name;
    select.appendChild(option);
  }
}

// Show select when input clicked
function showSelect(id){
  let select = document.getElementById(id);
  select.style.display = 'block';
}

// Filter options for search
function filterOptions(id){
  let input = document.getElementById(id+'Search').value.toLowerCase();
  let select = document.getElementById(id);
  select.innerHTML = '';
  for(let [code,name] of Object.entries(languages)){
    if(name.toLowerCase().includes(input)){
      let option = document.createElement('option');
      option.value = code;
      option.textContent = name;
      select.appendChild(option);
    }
  }
  select.style.display = 'block';
}

// Update input box when selecting a language
function updateInput(id){
  let select = document.getElementById(id);
  let input = document.getElementById(id+'Search');
  input.value = languages[select.value] || '';
  select.style.display = 'none';
}

// Swap source and target languages
function swap(){
  let srcSelect = document.getElementById('src');
  let tgtSelect = document.getElementById('tgt');
  let srcInput = document.getElementById('srcSearch');
  let tgtInput = document.getElementById('tgtSearch');

  // Swap select values
  let temp = srcSelect.value;
  srcSelect.value = tgtSelect.value;
  tgtSelect.value = temp;

  // Update input boxes from full dictionary
  srcInput.value = languages[srcSelect.value] || '';
  tgtInput.value = languages[tgtSelect.value] || '';
}

// Start recognition
function startRecognition(){
  const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
  recognition = new SpeechRecognition();
  recognition.lang = document.getElementById('src').value;
  recognition.interimResults = true;
  recognition.continuous = true;

  recognition.onresult = (event) => {
    let transcript = Array.from(event.results).map(r=>r[0].transcript).join('');
    screen.innerText = transcript;
    if(event.results[0].isFinal) translateAndSpeak(transcript);
  }

  recognition.start();
  listening = true;
  screen.innerText = "🎤 Listening...";
}

// Stop recognition
function stopRecognition(){
  if(recognition) recognition.stop();
  listening = false;
  screen.innerText = "🎤 Hold SPACE and speak to translate...";
}

// Translate & play audio from base64
async function translateAndSpeak(text){
  let src = document.getElementById('src').value;
  let tgt = document.getElementById('tgt').value;

  let res = await fetch('/translate',{
    method:'POST',
    headers:{'Content-Type':'application/json'},
    body: JSON.stringify({text,src,tgt})
  });

  let data = await res.json();
  let audioPlayer = document.getElementById('audioPlayer');
  audioPlayer.src = "data:audio/mp3;base64," + data.audio_base64;
  audioPlayer.play();
}

// Push-to-talk using SPACE
document.body.addEventListener("keydown",(e)=>{
  if(e.code==="Space" && !listening){
    startRecognition();
    e.preventDefault();
  }
});

document.body.addEventListener("keyup",(e)=>{
  if(e.code==="Space" && listening){
    stopRecognition();
    e.preventDefault();
  }
});

loadLanguages();
</script>
</body>
</html>


if __name__ == "__main__":
    app.run(debug=True)
