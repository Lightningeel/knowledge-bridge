# knowledge-bridge


Prevent repeated engineering mistakes and tackles the tribal knowledge gap—the missing context that leads teams to repeat the same mistake using persistent memory.

## The Problem

Engineering teams don’t forget code—they forget context.

Important decisions live in:
- Slack threads
- Incident calls
- Someone’s memory

This leads to repeated bugs, bad fixes, and slow onboarding.

## What This Does

This system:
- Captures live engineering decisions from real workflows
- Stores them as structured memory
- Retrieves relevant context during development
- Warns when a change conflicts with past decisions


## How It Works

1. Capture events (commits, meetings, incidents)
2. Extract decisions and context
3. Store structured memory + embeddings
4. Retrieve relevant past knowledge (RAG)
5. Intervene during development

## Example

You change retry logic in a service.

Immediate system response:
> "This change previously caused duplicate transactions (March 2024 incident)."

Suggested fix:
- Limit retries
- Add idempotency check

## Installation

git clone https://github.com/Lightningeel/knowledge-bridge


cd knowledge-bridge


pip install -r requirements.txt


## Usage

code.html

# or

start-agent --watch repo/

# proof of concept

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tribal Custodian — AI-Powered Institutional Memory</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;700&family=Space+Grotesk:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #060608;
    --surface: #0e0e14;
    --panel: #12121a;
    --border: #1a1a28;
    --border2: #222235;
    --accent: #6d28d9;
    --accent-glow: rgba(109,40,217,0.4);
    --cyan: #06b6d4;
    --danger: #dc2626;
    --danger-soft: rgba(220,38,38,0.1);
    --warning: #d97706;
    --warning-soft: rgba(217,119,6,0.1);
    --success: #059669;
    --success-soft: rgba(5,150,105,0.1);
    --text: #e8eaf0;
    --text2: #9ca3af;
    --text3: #4b5563;
    --import: #8be9fd;
  }
  * { margin:0; padding:0; box-sizing:border-box; }
  body { background:var(--bg); color:var(--text); font-family:'Space Grotesk',sans-serif; min-height:100vh; display:flex; flex-direction:column; overflow:hidden; }

  /* Ambient glows */
  .glow-tl { position:fixed; top:-150px; left:-150px; width:400px; height:400px; background:radial-gradient(circle, rgba(109,40,217,0.07) 0%, transparent 70%); pointer-events:none; z-index:0; }
  .glow-br { position:fixed; bottom:-150px; right:-150px; width:400px; height:400px; background:radial-gradient(circle, rgba(6,182,212,0.05) 0%, transparent 70%); pointer-events:none; z-index:0; }

  header { position:relative; z-index:10; display:flex; align-items:center; justify-content:space-between; padding:0 24px; height:52px; border-bottom:1px solid var(--border); background:rgba(14,14,20,0.95); backdrop-filter:blur(12px); }
  .logo { display:flex; align-items:center; gap:10px; font-weight:700; font-size:15px; }
  .logo-mark { width:28px; height:28px; background:linear-gradient(135deg,var(--accent),var(--cyan)); border-radius:7px; display:flex; align-items:center; justify-content:center; font-size:14px; box-shadow:0 0 16px var(--accent-glow); }
  .header-tabs { display:flex; gap:4px; }
  .htab { padding:6px 14px; border-radius:6px; font-size:12px; font-family:'JetBrains Mono',monospace; color:var(--text3); display:flex; align-items:center; gap:6px; cursor:pointer; }
  .htab.active { background:var(--border2); color:var(--text); }
  .htab-dot { width:6px; height:6px; border-radius:50%; background:var(--cyan); }
  .ai-badge { display:flex; align-items:center; gap:6px; padding:4px 12px; background:rgba(109,40,217,0.15); border:1px solid rgba(109,40,217,0.3); border-radius:20px; font-size:11px; font-weight:600; color:#a78bfa; }
  .ai-dot { width:6px; height:6px; border-radius:50%; background:#a78bfa; animation:aiPulse 1.5s ease-in-out infinite; }
  @keyframes aiPulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.4;transform:scale(0.8)} }

  .toolbar { position:relative; z-index:10; display:flex; align-items:center; justify-content:space-between; padding:0 24px; height:32px; border-bottom:1px solid var(--border); background:var(--surface); font-size:11px; font-family:'JetBrains Mono',monospace; color:var(--text3); }
  .breadcrumb { display:flex; align-items:center; gap:8px; color:var(--text2); }
  .breadcrumb span { color:var(--text3); }
  .scan-status { display:flex; align-items:center; gap:6px; }
  .scan-status.ok { color:var(--success); }
  .scan-status.scanning { color:var(--cyan); }
  .scan-status.alert { color:var(--danger); }
  .si { width:6px; height:6px; border-radius:50%; background:currentColor; }
  .si.pulse { animation:siPulse 1s ease-in-out infinite; }
  @keyframes siPulse { 0%,100%{opacity:1} 50%{opacity:0.3} }

  .workspace { position:relative; z-index:1; flex:1; display:grid; grid-template-columns:1fr 400px; grid-template-rows:1fr 200px; overflow:hidden; }

  /* Editor */
  .editor-pane { grid-row:1; grid-column:1; border-right:1px solid var(--border); border-bottom:1px solid var(--border); display:flex; flex-direction:column; }
  .pane-bar { display:flex; align-items:center; justify-content:space-between; padding:0 14px; height:34px; border-bottom:1px solid var(--border); background:var(--surface); font-size:11px; font-family:'JetBrains Mono',monospace; color:var(--text3); flex-shrink:0; }
  .pane-title { display:flex; align-items:center; gap:8px; color:var(--text2); }
  .wcs { display:flex; gap:5px; }
  .wc { width:10px; height:10px; border-radius:50%; }
  .wc-r{background:#ff5f57} .wc-y{background:#ffbd2e} .wc-g{background:#28ca41}
  .editor-inner { flex:1; display:flex; overflow:hidden; }
  .gutter { width:44px; padding:14px 8px; text-align:right; font-family:'JetBrains Mono',monospace; font-size:13px; line-height:1.75; color:var(--text3); border-right:1px solid var(--border); user-select:none; background:rgba(0,0,0,0.2); flex-shrink:0; overflow:hidden; }
  .code-in { flex:1; padding:14px; font-family:'JetBrains Mono',monospace; font-size:13px; line-height:1.75; background:transparent; border:none; outline:none; color:var(--import); resize:none; caret-color:var(--cyan); tab-size:4; }
  .code-in::placeholder { color:var(--text3); }

  /* AI stream */
  .ai-pane { grid-row:2; grid-column:1; border-right:1px solid var(--border); display:flex; flex-direction:column; background:var(--surface); }
  .ai-msgs { flex:1; padding:10px 14px; overflow-y:auto; display:flex; flex-direction:column; gap:8px; }
  .ai-msg { display:flex; gap:8px; animation:msgIn 0.3s ease; }
  @keyframes msgIn { from{opacity:0;transform:translateY(6px)} to{opacity:1;transform:translateY(0)} }
  .ai-av { width:22px; height:22px; border-radius:5px; background:linear-gradient(135deg,var(--accent),var(--cyan)); display:flex; align-items:center; justify-content:center; font-size:11px; flex-shrink:0; box-shadow:0 0 8px var(--accent-glow); }
  .ai-txt { font-size:11px; line-height:1.6; color:var(--text2); padding-top:2px; font-family:'JetBrains Mono',monospace; }
  .ai-txt .hl{color:#a78bfa} .ai-txt .er{color:#fca5a5} .ai-txt .ok{color:#6ee7b7} .ai-txt .cy{color:#67e8f9}
  .typing { display:flex; gap:4px; padding-top:5px; }
  .typing span { width:4px; height:4px; border-radius:50%; background:var(--accent); animation:tb 1.2s ease-in-out infinite; }
  .typing span:nth-child(2){animation-delay:0.2s} .typing span:nth-child(3){animation-delay:0.4s}
  @keyframes tb { 0%,100%{transform:translateY(0);opacity:0.4} 50%{transform:translateY(-4px);opacity:1} }

  /* Guardian */
  .guardian-pane { grid-row:1/3; grid-column:2; display:flex; flex-direction:column; background:var(--panel); overflow:hidden; }
  .g-idle { flex:1; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:18px; padding:32px; text-align:center; }
  .shield-ring { position:relative; width:72px; height:72px; }
  .sr-outer { position:absolute; inset:0; border-radius:50%; border:1px solid rgba(109,40,217,0.2); animation:rp 3s ease-in-out infinite; }
  .sr-inner { position:absolute; inset:10px; border-radius:50%; border:1px solid rgba(109,40,217,0.3); animation:rp 3s ease-in-out infinite 0.5s; }
  .sr-core { position:absolute; inset:20px; border-radius:50%; background:rgba(109,40,217,0.15); display:flex; align-items:center; justify-content:center; font-size:18px; }
  @keyframes rp { 0%,100%{transform:scale(1);opacity:1} 50%{transform:scale(1.08);opacity:0.5} }
  .g-idle-title { font-size:13px; font-weight:600; color:var(--text2); }
  .g-idle-sub { font-size:11px; color:var(--text3); font-family:'JetBrains Mono',monospace; }

  .kg-box { margin:0 16px 16px; padding:12px; background:rgba(0,0,0,0.3); border:1px solid var(--border); border-radius:8px; }
  .kg-ttl { font-size:10px; color:var(--text3); letter-spacing:1px; text-transform:uppercase; margin-bottom:8px; font-family:'JetBrains Mono',monospace; }
  .kg-row { display:flex; align-items:center; gap:8px; padding:4px 0; border-bottom:1px solid rgba(255,255,255,0.03); font-size:11px; font-family:'JetBrains Mono',monospace; }
  .kg-row:last-child{border-bottom:none}
  .kg-dot { width:6px; height:6px; border-radius:50%; flex-shrink:0; }
  .kg-name{color:var(--text2)} .kg-sev{color:var(--text3);margin-left:auto}

  .vcount { padding:10px 14px; border-bottom:1px solid var(--border); font-size:11px; font-family:'JetBrains Mono',monospace; display:flex; align-items:center; gap:8px; }
  .cbadge { display:inline-flex; align-items:center; justify-content:center; width:18px; height:18px; border-radius:50%; background:var(--danger); color:white; font-size:10px; font-weight:700; }
  .vlist { flex:1; overflow-y:auto; padding:12px; display:flex; flex-direction:column; gap:10px; }

  .vcard { border-radius:9px; overflow:hidden; border:1px solid var(--border2); animation:ca 0.5s cubic-bezier(0.34,1.56,0.64,1); }
  @keyframes ca { from{opacity:0;transform:translateY(14px) scale(0.96)} to{opacity:1;transform:translateY(0) scale(1)} }
  .vcard.critical{border-color:rgba(220,38,38,0.3)}
  .vcard.warn{border-color:rgba(217,119,6,0.3)}

  .vh { display:flex; align-items:center; justify-content:space-between; padding:10px 12px; border-bottom:1px solid var(--border); }
  .vh.critical{background:var(--danger-soft)} .vh.warn{background:var(--warning-soft)}
  .vlib { font-family:'JetBrains Mono',monospace; font-size:13px; font-weight:700; }
  .spill { font-size:9px; font-weight:700; letter-spacing:1.5px; padding:3px 7px; border-radius:4px; font-family:'JetBrains Mono',monospace; }
  .spill.critical{background:rgba(220,38,38,0.2);color:#fca5a5}
  .spill.warn{background:rgba(217,119,6,0.2);color:#fcd34d}

  .vb { padding:10px 12px; display:flex; flex-direction:column; gap:8px; background:var(--surface); }
  .vrule { font-size:12px; color:#e2e8f0; font-weight:500; line-height:1.5; }
  .vfacts { display:flex; flex-direction:column; gap:5px; }
  .fr { display:grid; grid-template-columns:18px 60px 1fr; gap:5px; font-size:11px; font-family:'JetBrains Mono',monospace; align-items:start; }
  .fi{font-size:11px} .fk{color:var(--text3)} .fv{color:var(--text2);line-height:1.4}
  .fv.p{color:#c4b5fd}

  .vr { padding:10px 12px; background:rgba(0,0,0,0.4); border-top:1px solid var(--border); font-size:11px; font-family:'JetBrains Mono',monospace; }
  .vr-lbl { color:#a78bfa; font-size:10px; letter-spacing:1px; text-transform:uppercase; margin-bottom:4px; }
  .vr-txt { color:var(--text2); line-height:1.6; }

  .clear-state { flex:1; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:12px; animation:ca 0.4s ease; }
  .clear-ring { width:60px; height:60px; border-radius:50%; background:var(--success-soft); border:2px solid rgba(5,150,105,0.4); display:flex; align-items:center; justify-content:center; font-size:22px; }

  ::-webkit-scrollbar{width:3px} ::-webkit-scrollbar-track{background:transparent} ::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px}

  .btmbar { position:relative; z-index:10; display:flex; align-items:center; justify-content:space-between; padding:0 20px; height:26px; background:var(--accent); font-size:11px; font-family:'JetBrains Mono',monospace; color:rgba(255,255,255,0.8); }
</style>
</head>
<body>
<div class="glow-tl"></div>
<div class="glow-br"></div>

<header>
  <div class="logo">
    <div class="logo-mark">🛡️</div>
    Tribal Custodian
  </div>
  <div class="header-tabs">
    <div class="htab active"><div class="htab-dot"></div>maya_feature.py</div>
    <div class="htab">knowledge_graph.json</div>
  </div>
  <div class="ai-badge"><div class="ai-dot"></div>AI REASONING ACTIVE</div>
</header>

<div class="toolbar">
  <div class="breadcrumb">vortex-backend <span>/</span> src <span>/</span> api <span>/</span> maya_feature.py</div>
  <div style="display:flex;align-items:center;gap:16px">
    <div class="scan-status ok" id="scanStatus"><div class="si"></div>Guardian watching</div>
    <span>Python 3.12</span>
  </div>
</div>

<div class="workspace">

  <div class="editor-pane">
    <div class="pane-bar">
      <div class="wcs"><div class="wc wc-r"></div><div class="wc wc-y"></div><div class="wc wc-g"></div></div>
      <div class="pane-title">📄 Code Editor — maya_feature.py</div>
      <span id="lineCol">Ln 1, Col 1</span>
    </div>
    <div class="editor-inner">
      <div class="gutter" id="gutter">1</div>
      <textarea class="code-in" id="codeIn" placeholder="# Start typing Maya's code here...&#10;# Try: import FastHTTP" spellcheck="false" autocomplete="off"></textarea>
    </div>
  </div>

  <div class="ai-pane">
    <div class="pane-bar">
      <div class="pane-title">🧠 AI Reasoning Stream</div>
      <span style="color:#a78bfa;font-size:10px">Hindsight Engine v1.0</span>
    </div>
    <div class="ai-msgs" id="aiMsgs">
      <div class="ai-msg">
        <div class="ai-av">🤖</div>
        <div class="ai-txt">Guardian initialised. Loaded <span class="hl">3 atomic norms</span> from knowledge graph. Temporal context: <span class="cy">April 2026</span>. Watching for violations...</div>
      </div>
    </div>
  </div>

  <div class="guardian-pane">
    <div class="pane-bar">
      <div class="pane-title">⚠️ Violations</div>
      <span id="vcLabel" style="color:var(--text3);font-size:11px">0 found</span>
    </div>
    <div id="guardianBody">
      <div class="g-idle">
        <div class="shield-ring"><div class="sr-outer"></div><div class="sr-inner"></div><div class="sr-core">🛡️</div></div>
        <div class="g-idle-title">No violations detected</div>
        <div class="g-idle-sub">Guardian is watching your code</div>
      </div>
      <div class="kg-box">
        <div class="kg-ttl">📊 Knowledge Graph — 3 norms loaded</div>
        <div class="kg-row"><div class="kg-dot" style="background:#ef4444"></div><span class="kg-name">FastHTTP</span><span class="kg-sev">CRITICAL</span></div>
        <div class="kg-row"><div class="kg-dot" style="background:#ef4444"></div><span class="kg-name">pickle</span><span class="kg-sev">CRITICAL</span></div>
        <div class="kg-row"><div class="kg-dot" style="background:#f59e0b"></div><span class="kg-name">requests</span><span class="kg-sev">WARNING</span></div>
      </div>
    </div>
  </div>

</div>

<div class="btmbar">
  <span>🛡️ Tribal Custodian — Hindsight Framework v1.0 · Hackathon 2026</span>
  <span id="btmRight">Guardian Active · 3 norms loaded</span>
</div>

<script>
/**
 * TRIBAL KNOWLEDGE DATABASE
 * Simulation of a Knowledge Graph extracted from Slack, transcripts, and post-mortems.
 */
const KG = [
  { 
    lib:"fasthttp", 
    display:"FastHTTP", 
    rule:"Do not import FastHTTP in any backend service", 
    reason:"Critical security vulnerability: exposes auth tokens via header injection during HTTP requests.", 
    flaggedBy:"Arjun", 
    flaggedDate:"2025-05-14", 
    context:"Vortex Project — backend auth service", 
    source:"Slack #backend-alerts + Team huddle recording", 
    severity:"CRITICAL", 
    reasoning:"FastHTTP caused a P0 production incident. Arjun traced it to a header injection flaw leaking JWT tokens. The library has not patched this upstream. Still applies in 2026." 
  },
  { 
    lib:"pickle", 
    display:"pickle", 
    rule:"Do not use pickle for inter-service serialization", 
    reason:"Caused silent data corruption between Python 3.10 and 3.12 services. No exception thrown.", 
    flaggedBy:"Sana", 
    flaggedDate:"2025-02-20", 
    context:"Microservices communication layer", 
    source:"Engineering all-hands transcript (Feb 2025)", 
    severity:"CRITICAL", 
    reasoning:"Pickle byte format changed between Python minor versions. Sana documented silent data corruption in 3 microservices with no exceptions raised. Use msgpack or JSON for cross-service payloads." 
  },
  { 
    lib:"requests", 
    display:"requests", 
    rule:"Avoid requests library inside AWS Lambda functions", 
    reason:"Cold start latency spikes of 800ms+ in production. Affected checkout flow SLA.", 
    flaggedBy:"Priya", 
    flaggedDate:"2024-11-03", 
    context:"AWS Lambda — checkout service", 
    source:"Post-mortem doc + Slack #infra-incidents", 
    severity:"WARNING", 
    reasoning:"requests is synchronous and adds 800ms cold start overhead in Lambda. Priya benchmarked httpx async at 120ms. Switch to httpx with async/await patterns." 
  }
];

const codeIn = document.getElementById('codeIn');
const gutter = document.getElementById('gutter');
const aiMsgs = document.getElementById('aiMsgs');
const scanStatus = document.getElementById('scanStatus');
const vcLabel = document.getElementById('vcLabel');
const btmRight = document.getElementById('btmRight');
const lineCol = document.getElementById('lineCol');
const guardianBody = document.getElementById('guardianBody');

let timeout = null;
let lastLibs = [];

// Updates the line numbers in the gutter
function updateGutter() {
  const n = codeIn.value.split('\n').length;
  gutter.innerHTML = Array.from({length:n},(_,i)=>i+1).join('<br>');
}

// Simulates AI message stream
function addMsg(html, delay=0) {
  return new Promise(r => setTimeout(() => {
    const d = document.createElement('div');
    d.className = 'ai-msg';
    d.innerHTML = `<div class="ai-av">🤖</div><div class="ai-txt">${html}</div>`;
    aiMsgs.appendChild(d);
    aiMsgs.scrollTop = aiMsgs.scrollHeight;
    r();
  }, delay));
}

function addTyping() {
  const d = document.createElement('div');
  d.className = 'ai-msg'; d.id = 'typing';
  d.innerHTML = `<div class="ai-av">🤖</div><div class="ai-txt"><div class="typing"><span></span><span></span><span></span></div></div>`;
  aiMsgs.appendChild(d);
  aiMsgs.scrollTop = aiMsgs.scrollHeight;
}

function removeTyping() { const e = document.getElementById('typing'); if(e) e.remove(); }

// Checks code against the Knowledge Graph
function check(code) { const l = code.toLowerCase(); return KG.filter(n=>l.includes(n.lib)); }

// Renders the violation cards in the right pane
function renderGuardian(vs) {
  if(vs.length===0 && codeIn.value.trim().length>0) {
    vcLabel.textContent='0 found'; vcLabel.style.color='var(--success)';
    scanStatus.className='scan-status ok'; scanStatus.innerHTML=`<div class="si"></div> All clear`;
    btmRight.textContent='✅ No violations — safe to commit';
    guardianBody.innerHTML=`<div class="clear-state"><div class="clear-ring">✅</div><div style="font-size:13px;font-weight:600;color:#6ee7b7">All Clear!</div><div style="font-size:11px;color:var(--text3);font-family:'JetBrains Mono',monospace">No tribal knowledge violations</div></div>`;
    return;
  }
  if(vs.length===0) {
    vcLabel.textContent='0 found'; vcLabel.style.color='var(--text3)';
    guardianBody.innerHTML=`<div class="g-idle"><div class="shield-ring"><div class="sr-outer"></div><div class="sr-inner"></div><div class="sr-core">🛡️</div></div><div class="g-idle-title">No violations detected</div><div class="g-idle-sub">Guardian is watching your code</div></div><div class="kg-box"><div class="kg-ttl">📊 Knowledge Graph — 3 norms loaded</div><div class="kg-row"><div class="kg-dot" style="background:#ef4444"></div><span class="kg-name">FastHTTP</span><span class="kg-sev">CRITICAL</span></div><div class="kg-row"><div class="kg-dot" style="background:#ef4444"></div><span class="kg-name">pickle</span><span class="kg-sev">CRITICAL</span></div><div class="kg-row"><div class="kg-dot" style="background:#f59e0b"></div><span class="kg-name">requests</span><span class="kg-sev">WARNING</span></div></div>`;
    return;
  }
  vcLabel.textContent=`${vs.length} found`; vcLabel.style.color='var(--danger)';
  scanStatus.className='scan-status alert'; scanStatus.innerHTML=`<div class="si pulse"></div> ${vs.length} violation${vs.length>1?'s':''} detected`;
  btmRight.textContent=`⚠️ ${vs.length} violation${vs.length>1?'s':''} — fix before committing`;
  let h=`<div class="vcount"><div class="cbadge">${vs.length}</div> Violation${vs.length>1?'s':''} requiring attention</div><div class="vlist">`;
  for(const v of vs){
    const c=v.severity==='CRITICAL'?'critical':'warn';
    h+=`<div class="vcard ${c}"><div class="vh ${c}"><span class="vlib">${v.display}</span><span class="spill ${c}">${v.severity}</span></div><div class="vb"><div class="vrule">❌ ${v.rule}</div><div class="vfacts"><div class="fr"><span class="fi">💡</span><span class="fk">reason</span><span class="fv">${v.reason}</span></div><div class="fr"><span class="fi">👤</span><span class="fk">flagged</span><span class="fv p">${v.flaggedBy} · ${v.flaggedDate}</span></div><div class="fr"><span class="fi">📍</span><span class="fk">context</span><span class="fv">${v.context}</span></div><div class="fr"><span class="fi">🗂️</span><span class="fk">source</span><span class="fv">${v.source}</span></div></div></div><div class="vr"><div class="vr-lbl">🧠 AI Reasoning</div><div class="vr-txt">${v.reasoning}</div></div></div>`;
  }
  h+=`</div>`;
  guardianBody.innerHTML=h;
}

// Logic for analyzing new violations and updating the stream
async function analyse(vs, newVs) {
  scanStatus.className='scan-status scanning'; scanStatus.innerHTML=`<div class="si pulse"></div> Analysing...`;
  addTyping();
  await new Promise(r=>setTimeout(r,750));
  removeTyping();
  if(vs.length===0 && codeIn.value.trim().length>0) {
    await addMsg(`✅ <span class="ok">Analysis complete.</span> Code checked against <span class="hl">3 atomic norms</span>. No violations found.`);
  } else {
    for(const v of newVs) {
      await addMsg(`🚨 <span class="er">Violation:</span> <span class="hl">${v.display}</span> matches norm flagged by <span class="cy">${v.flaggedBy}</span> on ${v.flaggedDate}. Severity: ${v.severity}.`);
    }
  }
  renderGuardian(vs);
  lastLibs = vs.map(v=>v.lib);
}

// Event Listeners for Editor functionality
codeIn.addEventListener('input', () => {
  updateGutter();
  clearTimeout(timeout);
  scanStatus.className='scan-status scanning'; scanStatus.innerHTML=`<div class="si pulse"></div> Watching...`;
  timeout = setTimeout(async () => {
    const vs = check(codeIn.value);
    const newVs = vs.filter(v=>!lastLibs.includes(v.lib));
    const libs = vs.map(v=>v.lib);
    if(JSON.stringify(libs)!==JSON.stringify(lastLibs)) { await analyse(vs,newVs); }
    else { scanStatus.className='scan-status ok'; scanStatus.innerHTML=`<div class="si"></div> Guardian watching`; }
  }, 800);
});

codeIn.addEventListener('keydown', e => {
  if(e.key==='Tab'){
    e.preventDefault();
    const s=codeIn.selectionStart;
    codeIn.value=codeIn.value.substring(0,s)+'    '+codeIn.value.substring(codeIn.selectionEnd);
    codeIn.selectionStart=codeIn.selectionEnd=s+4;
  }
  updateGutter();
  const t=codeIn.value.substring(0,codeIn.selectionStart).split('\n');
  lineCol.textContent=`Ln ${t.length}, Col ${t[t.length-1].length+1}`;
});

codeIn.addEventListener('click',()=>{
  const t=codeIn.value.substring(0,codeIn.selectionStart).split('\n');
  lineCol.textContent=`Ln ${t.length}, Col ${t[t.length-1].length+1}`;
});

// Initialization
updateGutter();
</script>
</body>
</html>
```






## Tech Stack

- Python
- Node.js
- Vector DB


## Memory Model

Each event is stored as a structured memory object that captures not just *what happened*, but *why it happened* and *what decision was made*.

### Core Structure
```
{
  id: "uuid",
  event_type: "incident | code_change | decision | discussion",
  source: "git | slack | meeting | system",

  service: "payments-service",
  component: "retry-handler",
  environment: "production | staging | dev",

  timestamp: "2026-04-13T10:30:00Z",
  actors: ["user_id_1", "user_id_2"],

  symptom: "duplicate transactions observed",
  root_cause: "retry logic triggered multiple times without idempotency",

  decision: "limit retries to 2 and enforce idempotency keys",
  alternatives_considered: [
    "disable retries completely",
    "increase timeout instead of retrying"
  ],

  context: {
    load: "high traffic",
    dependency: "third-party payment gateway timeout",
    related_services: ["order-service", "gateway-adapter"]
  },

  code_refs: {
    files: ["src/payments/retry.py"],
    commits: ["abc123", "def456"],
    pull_requests: [42]
  },

  discussion_refs: {
    slack_threads: ["slack-link-1"],
    meeting_ids: ["meeting-xyz"]
  },

  tags: ["retry-logic", "idempotency", "payments", "incident"],

  outcome: "resolved",
  impact: {
    severity: "high",
    user_impact: "duplicate charges",
    duration_minutes: 45
  },

  confidence_score: 0.87,
  validation_status: "verified | inferred",

  embedding: [ ... vector representation ... ]
}

```
- Episodic Memory → specific incidents and debugging sessions
- Decision Memory → explicit engineering decisions and tradeoffs
- Semantic Memory → generalized patterns (e.g., "retry issues in payments")
- Procedural Memory → known fixes and best practices

- semantic similarity (embeddings)
- metadata filters (service, component, environment)
- temporal relevance (recent vs historical incidents)


Current Change:
- Increasing retry count to 5

Retrieved Memory:
- Past incident caused by excessive retries

System Response:
> "This change conflicts with a previous incident (duplicate transactions due to retries)."



1. Event captured (commit, meeting, incident)
2. Context + decision extracted
3. Structured memory created
4. Embedding generated
5. Stored in memory layer
6. Retrieved during future decisions
7. Updated based on outcome




## Limitations

- Depends on quality of captured data
- May miss implicit decisions
- Requires careful privacy controls