# index.html
Interactive Virtual Chemistry Laboratory and reaction simulator for schools.
<!DOCTYPE html>
<html lang="tk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wirtual Himiýa Laboratoriýasy | Alparslan Myradow</title>
    <style>
        :root {
            --bg-dark: #0f172a;
            --card-bg: #1e293b;
            --accent: #3b82f6;
            --accent-hover: #2563eb;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: var(--bg-dark); color: var(--text-main); min-height: 100vh; padding-bottom: 40px; }
        header { background-color: var(--card-bg); padding: 20px; text-align: center; border-bottom: 2px solid var(--accent); box-shadow: 0 4px 6px -1px rgba(0,0,0,0.3); }
        h1 { font-size: 24px; color: var(--accent); margin-bottom: 5px; }
        .author { font-size: 14px; color: var(--warning); font-weight: bold; }
        .container { max-width: 900px; margin: 20px auto; padding: 0 15px; }
        .card { background-color: var(--card-bg); border-radius: 12px; padding: 20px; margin-bottom: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.2); border: 1px solid #334155; }
        .section-title { font-size: 18px; margin-bottom: 15px; color: var(--text-main); display: flex; align-items: center; gap: 8px; }
        .reagents-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(130px, 1fr)); gap: 10px; margin-bottom: 15px; }
        .reagent-btn { background-color: #334155; color: white; border: 2px solid transparent; padding: 10px; border-radius: 8px; cursor: pointer; font-size: 14px; transition: 0.2s; text-align: center; }
        .reagent-btn:hover { background-color: #475569; }
        .reagent-btn.selected { border-color: var(--accent); background-color: #1d4ed8; font-weight: bold; }
        .lab-bench { display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 220px; background: #090d16; border-radius: 10px; position: relative; overflow: hidden; border: 1px dashed #475569; padding: 20px; }
        /* Kolba animasiýasy */
        .flask { width: 90px; height: 110px; border: 4px solid #cbd5e1; border-top: none; border-radius: 0 0 45px 45px; position: relative; display: flex; align-items: flex-end; justify-content: center; overflow: hidden; background: rgba(255,255,255,0.03); margin-bottom: 10px; }
        .flask::before { content: ''; position: absolute; top: -25px; width: 35px; height: 25px; border: 4px solid #cbd5e1; border-bottom: none; left: 23px; }
        .liquid { width: 100%; height: 50%; background-color: rgba(59, 130, 246, 0.4); transition: background-color 0.5s, height 0.5s; position: relative; }
        .bubbles { position: absolute; width: 10px; height: 10px; background: white; border-radius: 50%; bottom: 10px; left: 40px; opacity: 0; }
        .flask.bubbling .bubbles { animation: bubbleUp 1s infinite; }
        @keyframes bubbleUp { 0% { bottom: 10px; opacity: 1; transform: scale(0.6); } 100% { bottom: 80px; opacity: 0; transform: scale(1.2); } }
        
        .action-btns { display: flex; gap: 10px; margin-top: 15px; width: 100%; }
        .btn { flex: 1; padding: 12px; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 15px; }
        .btn-mix { background-color: var(--success); color: white; }
        .btn-mix:hover { background-color: #059669; }
        .btn-reset { background-color: var(--danger); color: white; }
        .btn-reset:hover { background-color: #dc2626; }
        
        .result-box { background-color: #0f172a; padding: 15px; border-radius: 8px; border-left: 5px solid var(--accent); margin-top: 15px; font-size: 15px; line-height: 1.5; }
        .hints-container { display: flex; gap: 8px; flex-wrap: wrap; margin-top: 10px; }
        .hint-chip { background-color: #1e3a8a; color: #93c5fd; padding: 6px 12px; border-radius: 20px; font-size: 13px; cursor: pointer; border: 1px solid #3b82f6; }
        .hint-chip:hover { background-color: #2563eb; color: white; }
    </style>
</head>
<body>
    <header>
        <h1>🧪 Wirtual Himiýa Laboratoriýasy</h1>
        <div class="author">Dörediji we Awtor: Alparslan Myradow</div>
    </header>

    <div class="container">
        <!-- Reagentler -->
        <div class="card">
            <div class="section-title">1️⃣ 1-nji Reaktiwy saýlaň:</div>
            <div class="reagents-grid" id="r1Grid"></div>
            
            <div id="hintSection" style="display:none; margin-top: 15px;">
                <div style="font-size: 13px; color: var(--text-muted); margin-bottom: 5px;">💡 Maslahat (Reaksiýa girip biljek maddalar):</div>
                <div class="hints-container" id="hintsList"></div>
            </div>
        </div>

        <div class="card">
            <div class="section-title">2️⃣ 2-nji Reaktiwy saýlaň:</div>
            <div class="reagents-grid" id="r2Grid"></div>
        </div>

        <!-- Laboratoriýa Stoly -->
        <div class="card">
            <div class="section-title">🔬 Tejribe Kolbasy</div>
            <div class="lab-bench">
                <div class="flask" id="flaskElem">
                    <div class="liquid" id="liquidElem"></div>
                    <div class="bubbles"></div>
                </div>
                <div style="color: var(--text-muted); font-size: 14px;" id="statusText">Maddalary saýlaň we garyşdyryň</div>
            </div>
            <div class="action-btns">
                <button class="btn btn-mix" onclick="mixReagents()">Test Ed / Garyşdyr</button>
                <button class="btn btn-reset" onclick="resetLab()">Arassala</button>
            </div>
            <div class="result-box" id="resultBox">
                ✨ Netije: Hemişelik taýýar. Reagentleri saýlap barlap görüň!
            </div>
        </div>
    </div>

    <script>
        const reagents = [
            { id: 'HCl', name: 'HCI (Hlorid turşy)' },
            { id: 'NaOH', name: 'NaOH (Natriý gidroksid)' },
            { id: 'CuSO4', name: 'CuSO4 (Mis kuporosy)' },
            { id: 'Zn', name: 'Zn (Sink reňki)' },
            { id: 'AgNO3', name: 'AgNO3 (Kümüş nitrat)' },
            { id: 'H2SO4', name: 'H2SO4 (Kükürt turşy)' },
            { id: 'BaCl2', name: 'BaCl2 (Bariy hlorid)' },
            { id: 'Fenolftalein', name: 'Fenolftalein (İndikator)' }
        ];

        const reactions = {
            'HCl+NaOH': { eq: 'HCl + NaOH → NaCl + H₂O', desc: 'Neytralizasiýa reaksiýasy. Suw we duz emele geldi, ergin reňksiz.', color: 'rgba(255,255,255,0.2)', bubble: false },
            'HCl+Zn': { eq: '2HCl + Zn → ZnCl₂ + H₂↑', desc: 'Gaz çykма (Wodorod gazy). Köpürjikler peýda boldy we sink eredi!', color: 'rgba(200,220,255,0.6)', bubble: true },
            'CuSO4+NaOH': { eq: 'CuSO₄ + 2NaOH → Cu(OH)₂↓ + Na₂SO₄', desc: 'Gök reňkli çökündi (Mis gidroksid) emele geldi!', color: 'rgba(0, 100, 255, 0.8)', bubble: false },
            'HCl+AgNO3': { eq: 'HCl + AgNO₃ → AgCl↓ + HNO₃', desc: 'Ak reňkli pamyk görnüşli çökündi (Kümüş hlorid) düşdi!', color: 'rgba(240, 240, 240, 0.9)', bubble: false },
            'H2SO4+BaCl2': { eq: 'H₂SO₄ + BaCl₂ → BaSO₄↓ + 2HCl', desc: 'Agyr ak reňkli çökündi (Bariy sulfat) emele geldi!', color: 'rgba(255, 255, 255, 0.95)', bubble: false },
            'NaOH+Fenolftalein': { eq: 'NaOH + Indikator → Mähelreňk (Al-qyzyl ergin)', desc: 'Ishgar sredada fenolftalein goýy mähelreňk (malina) reňke öwrüldi!', color: 'rgba(236, 72, 153, 0.7)', bubble: false }
        };

        let selectedR1 = null;
        let selectedR2 = null;

        function initGrids() {
            const r1Grid = document.getElementById('r1Grid');
            const r2Grid = document.getElementById('r2Grid');
            r1Grid.innerHTML = ''; r2Grid.innerHTML = '';

            reagents.forEach(r => {
                r1Grid.innerHTML += `<button class="reagent-btn" onclick="selectR1('${r.id}', this)">${r.name}</button>`;
                r2Grid.innerHTML += `<button class="reagent-btn" onclick="selectR2('${r.id}', this)">${r.name}</button>`;
            });
        }

        function selectR1(id, btn) {
            document.querySelectorAll('#r1Grid .reagent-btn').forEach(b => b.classList.remove('selected'));
            btn.classList.add('selected');
            selectedR1 = id;
            showHints(id);
        }

        function selectR2(id, btn) {
            document.querySelectorAll('#r2Grid .reagent-btn').forEach(b => b.classList.remove('selected'));
            btn.classList.add('selected');
            selectedR2 = id;
        }

        function showHints(r1) {
            const hintSection = document.getElementById('hintSection');
            const hintsList = document.getElementById('hintsList');
            hintsList.innerHTML = '';
            
            // Tapylýan mümkin 2-nji maddalar
            const compatible = reagents.filter(r => reactions[r1 + '+' + r.id] || reactions[r.id + '+' + r1]);
            
            if (compatible.length > 0) {
                hintSection.style.display = 'block';
                compatible.forEach(c => {
                    hintsList.innerHTML += `<div class="hint-chip" onclick="quickSelectR2('${c.id}')">${c.name}</div>`;
                });
            } else {
                hintSection.style.display = 'none';
            }
        }

        function quickSelectR2(id) {
            selectedR2 = id;
            const buttons = document.querySelectorAll('#r2Grid .reagent-btn');
            buttons.forEach(b => {
                if (b.innerText.includes(id)) {
                    document.querySelectorAll('#r2Grid .reagent-btn').forEach(btn => btn.classList.remove('selected'));
                    b.classList.add('selected');
                }
            });
        }

        function mixReagents() {
            const flask = document.getElementById('flaskElem');
            const liquid = document.getElementById('liquidElem');
            const statusText = document.getElementById('statusText');
            const resultBox = document.getElementById('resultBox');

            if (!selectedR1 || !selectedR2) {
                alert("Ily hem 1-nji, hem-de 2-nji reaktiwy saýlaň!");
                return;
            }

            const key1 = selectedR1 + '+' + selectedR2;
            const key2 = selectedR2 + '+' + selectedR1;
            const reaction = reactions[key1] || reactions[key2];

            flask.classList.add('bubbling');

            if (reaction) {
                liquid.style.backgroundColor = reaction.color;
                statusText.innerText = "Reaksiýa gidýär...";
                setTimeout(() => {
                    flask.classList.remove('bubbling');
                    statusText.innerText = "Reaksiýa tamamlandy!";
                    resultBox.innerHTML = `<strong>🧪 Deňleme:</strong> ${reaction.eq}<br><strong>📝 Netije:</strong> ${reaction.desc}`;
                }, 1000);
            } else {
                liquid.style.backgroundColor = 'rgba(150, 150, 150, 0.4)';
                setTimeout(() => {
                    flask.classList.remove('bubbling');
                    statusText.innerText = "Täsirleşme ýok";
                    resultBox.innerHTML = `⚠️ Bu maddalaryň arasynda bildirilen şertde hкимиýi reaksiýa ýok ýa-da öwrenilmeýär. Başga reaktiw synap görüň!`;
                }, 1000);
            }
        }

        function resetLab() {
            selectedR1 = null; selectedR2 = null;
            document.querySelectorAll('.reagent-btn').forEach(b => b.classList.remove('selected'));
            document.getElementById('flaskElem').classList.remove('bubbling');
            document.getElementById('liquidElem').style.backgroundColor = 'rgba(59, 130, 246, 0.4)';
            document.getElementById('statusText').innerText = "Maddalary saýlaň we garyşdyryň";
            document.getElementById('resultBox').innerHTML = "✨ Netije: Hemişelik taýýar. Reagentleri saýlap barlap görüň!";
            document.getElementById('hintSection').style.display = 'none';
        }

        initGrids();
    </script>
</body>
</html>
