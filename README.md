<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Small Habits, Big Impact — Electricity &amp; CO₂ Calculator</title>
<style>
  :root {
    --green: #1b7f4b;
    --green-dark: #0f5a34;
    --green-soft: #e6f4ec;
    --blue: #1565a8;
    --blue-dark: #0d4676;
    --blue-soft: #e5f0fa;
    --ink: #14232e;
    --muted: #4a5b68;
    --line: #d5e0e8;
    --bg: #ffffff;
    --panel: #f6fafc;
  }
  * { box-sizing: border-box; }
  html, body { margin: 0; }
  body {
    font-family: "Segoe UI", "Hiragino Sans", "Yu Gothic", Meiryo, system-ui, -apple-system, Arial, sans-serif;
    background: var(--bg);
    color: var(--ink);
    line-height: 1.5;
  }
  .wrap { max-width: 1200px; margin: 0 auto; padding: 28px 24px 40px; }

  header {
    border-left: 8px solid var(--green);
    padding: 4px 0 4px 20px;
    margin-bottom: 28px;
  }
  header h1 { margin: 0; font-size: clamp(1.7rem, 3.4vw, 2.6rem); line-height: 1.15; color: var(--green-dark); }
  header h1 span { color: var(--blue); }
  header p { margin: 8px 0 0; font-size: 1.1rem; color: var(--muted); max-width: 70ch; }

  .grid { display: grid; grid-template-columns: 5fr 7fr; gap: 24px; align-items: start; }
  @media (max-width: 900px) { .grid { grid-template-columns: 1fr; } }

  .panel {
    background: var(--panel);
    border: 2px solid var(--line);
    border-radius: 14px;
    padding: 22px;
  }
  .panel h2 { margin: 0 0 16px; font-size: 1.25rem; color: var(--blue-dark); }

  /* ---------- Inputs ---------- */
  .field { margin-bottom: 20px; }
  .field-top { display: flex; align-items: center; justify-content: space-between; gap: 10px; margin-bottom: 6px; }
  .field label { display: flex; align-items: center; gap: 9px; font-weight: 600; font-size: 1.02rem; }
  .field label svg { width: 26px; height: 26px; flex: none; }
  .numbox { display: flex; align-items: baseline; gap: 5px; }
  .numbox input[type=number] {
    width: 92px; font: inherit; font-weight: 700; font-size: 1.15rem; text-align: right;
    padding: 4px 8px; border: 2px solid var(--line); border-radius: 8px; background: #fff; color: var(--ink);
  }
  .numbox .unit { color: var(--muted); font-size: 0.95rem; min-width: 52px; }
  input:focus-visible, button:focus-visible { outline: 3px solid var(--blue); outline-offset: 2px; }

  input[type=range] {
    -webkit-appearance: none; appearance: none; width: 100%; height: 10px; border-radius: 6px;
    background: linear-gradient(to right, var(--green) var(--fill, 50%), #cfdde6 var(--fill, 50%));
    cursor: pointer; margin: 6px 0 2px;
  }
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none; width: 26px; height: 26px; border-radius: 50%;
    background: #fff; border: 4px solid var(--green); box-shadow: 0 1px 4px rgba(0,0,0,.3);
  }
  input[type=range]::-moz-range-thumb {
    width: 18px; height: 18px; border-radius: 50%; background: #fff; border: 4px solid var(--green);
  }
  .range-ends { display: flex; justify-content: space-between; font-size: 0.82rem; color: var(--muted); }

  .reset {
    width: 100%; margin-top: 6px; padding: 14px 18px; font: inherit; font-weight: 700; font-size: 1.1rem;
    color: #fff; background: var(--blue); border: 0; border-radius: 10px; cursor: pointer;
    display: flex; align-items: center; justify-content: center; gap: 10px;
  }
  .reset:hover { background: var(--blue-dark); }
  .reset svg { width: 22px; height: 22px; }

  /* ---------- Results ---------- */
  .cards { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; margin-bottom: 20px; }
  @media (max-width: 640px) { .cards { grid-template-columns: 1fr; } }
  .card { border-radius: 12px; padding: 16px; border: 2px solid; background: #fff; }
  .card.people { border-color: var(--blue); }
  .card.energy { border-color: var(--blue); background: var(--blue-soft); }
  .card.co2 { border-color: var(--green); background: var(--green-soft); }
  .card .icon { width: 34px; height: 34px; margin-bottom: 4px; }
  .card .label { font-size: 0.95rem; color: var(--muted); font-weight: 600; }
  .card .value { font-size: clamp(1.9rem, 3.6vw, 2.8rem); font-weight: 800; line-height: 1.1; margin: 4px 0 2px; font-variant-numeric: tabular-nums; word-break: break-word; }
  .card.energy .value { color: var(--blue-dark); }
  .card.co2 .value { color: var(--green-dark); }
  .card.people .value { color: var(--ink); }
  .card .sub { font-size: 0.92rem; color: var(--muted); }

  .secondary { display: flex; flex-wrap: wrap; gap: 14px; margin-bottom: 20px; }
  .secondary .tile { flex: 1 1 180px; background: #fff; border: 2px solid var(--line); border-radius: 12px; padding: 12px 16px; }
  .tile .label { font-size: 0.9rem; color: var(--muted); font-weight: 600; }
  .tile .value { font-size: 1.6rem; font-weight: 800; color: var(--green-dark); font-variant-numeric: tabular-nums; }

  /* ---------- Chart ---------- */
  .chart-box { background: #fff; border: 2px solid var(--line); border-radius: 12px; padding: 16px; }
  .chart-box h3 { margin: 0 0 4px; font-size: 1.05rem; color: var(--blue-dark); }
  .chart-box .note { margin: 0 0 8px; font-size: 0.88rem; color: var(--muted); }
  .bars { display: flex; flex-direction: column; gap: 14px; }
  .bar-row .bar-head { display: flex; justify-content: space-between; font-weight: 600; font-size: 0.98rem; margin-bottom: 4px; }
  .track { background: #e8eff4; border-radius: 8px; height: 34px; overflow: hidden; }
  .bar { height: 100%; width: 0; border-radius: 8px; transition: width .35s ease; min-width: 4px; }
  .bar.kwh { background: var(--blue); }
  .bar.kg { background: var(--green); }
  @media (prefers-reduced-motion: reduce) { .bar { transition: none; } }

  /* ---------- Explanation ---------- */
  .explain { margin-top: 24px; display: grid; gap: 14px; }
  .message {
    background: var(--green-soft); border-left: 8px solid var(--green); border-radius: 10px;
    padding: 16px 20px; font-size: 1.2rem; font-weight: 600; color: var(--green-dark);
  }
  .disclaimer {
    background: #fff; border: 2px solid var(--line); border-radius: 10px; padding: 14px 18px;
    font-size: 0.95rem; color: var(--muted);
  }
  .disclaimer strong { color: var(--ink); }

  /* Larger text for projectors */
  @media (min-width: 1400px) { body { font-size: 18px; } }
</style>
</head>
<body>
<div class="wrap">

  <header>
    <h1>Small Habits, <span>Big Impact</span> — Electricity &amp; CO₂ Calculator</h1>
    <p>See how everyday electricity-saving habits, shared by many people, could add up to less electricity used and fewer CO₂ emissions in Japan.</p>
  </header>

  <div class="grid">

    <!-- ================= INPUTS ================= -->
    <section class="panel" aria-labelledby="inputs-title">
      <h2 id="inputs-title">Adjust the habits</h2>

      <!-- Number of people -->
      <div class="field">
        <div class="field-top">
          <label for="people-num">
            <svg viewBox="0 0 32 32" aria-hidden="true"><circle cx="11" cy="10" r="5" fill="#1565a8"/><path d="M2 27c0-5.5 4-9 9-9s9 3.5 9 9z" fill="#1565a8"/><circle cx="23" cy="12" r="4" fill="#1b7f4b"/><path d="M17 27c0-4 2.6-7 6-7s7 3 7 7z" fill="#1b7f4b"/></svg>
            Number of people
          </label>
          <div class="numbox"><input type="number" id="people-num" data-key="people"><span class="unit">people</span></div>
        </div>
        <input type="range" id="people" data-key="people" aria-label="Number of people">
        <div class="range-ends"><span>10</span><span>1,000</span></div>
      </div>

      <!-- Watts saved -->
      <div class="field">
        <div class="field-top">
          <label for="watts-num">
            <svg viewBox="0 0 32 32" aria-hidden="true"><path d="M18 2L6 18h8l-2 12 12-17h-8z" fill="#e6a700" stroke="#b37f00" stroke-width="1.2" stroke-linejoin="round"/></svg>
            Electricity saved per person
          </label>
          <div class="numbox"><input type="number" id="watts-num" data-key="watts"><span class="unit">W</span></div>
        </div>
        <input type="range" id="watts" data-key="watts" aria-label="Watts saved per person">
        <div class="range-ends"><span>5 W</span><span>60 W</span></div>
      </div>

      <!-- Hours per day -->
      <div class="field">
        <div class="field-top">
          <label for="hours-num">
            <svg viewBox="0 0 32 32" aria-hidden="true"><circle cx="16" cy="16" r="12" fill="none" stroke="#1565a8" stroke-width="3"/><path d="M16 8v8l5 3" fill="none" stroke="#1565a8" stroke-width="3" stroke-linecap="round"/></svg>
            Hours saved per day
          </label>
          <div class="numbox"><input type="number" id="hours-num" data-key="hours"><span class="unit">hours</span></div>
        </div>
        <input type="range" id="hours" data-key="hours" aria-label="Hours saved per day">
        <div class="range-ends"><span>1 h</span><span>12 h</span></div>
      </div>

      <!-- Days per year -->
      <div class="field">
        <div class="field-top">
          <label for="days-num">
            <svg viewBox="0 0 32 32" aria-hidden="true"><rect x="4" y="7" width="24" height="21" rx="3" fill="none" stroke="#1565a8" stroke-width="3"/><path d="M4 14h24M10 3v7M22 3v7" stroke="#1565a8" stroke-width="3" stroke-linecap="round"/></svg>
            Days per year
          </label>
          <div class="numbox"><input type="number" id="days-num" data-key="days"><span class="unit">days</span></div>
        </div>
        <input type="range" id="days" data-key="days" aria-label="Days per year">
        <div class="range-ends"><span>30</span><span>365</span></div>
      </div>

      <!-- Emission factor -->
      <div class="field">
        <div class="field-top">
          <label for="factor-num">
            <svg viewBox="0 0 32 32" aria-hidden="true"><path d="M9 22a6 6 0 0 1 .5-12 8 8 0 0 1 15 2.5A5 5 0 0 1 24 22z" fill="#1b7f4b"/><text x="16" y="19.5" text-anchor="middle" font-size="8.5" font-weight="700" fill="#fff" font-family="Arial">CO₂</text></svg>
            CO₂ emission factor
          </label>
          <div class="numbox"><input type="number" id="factor-num" data-key="factor" step="0.01"><span class="unit">kg/kWh</span></div>
        </div>
        <input type="range" id="factor" data-key="factor" aria-label="CO2 emission factor">
        <div class="range-ends"><span>0.10</span><span>1.00</span></div>
      </div>

      <button class="reset" id="reset" type="button">
        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 12a8 8 0 1 0 2.5-5.8M4 4v5h5" fill="none" stroke="#fff" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
        Reset to defaults
      </button>
    </section>

    <!-- ================= RESULTS ================= -->
    <section aria-labelledby="results-title">
      <div class="panel">
        <h2 id="results-title">Estimated yearly impact</h2>

        <div class="cards" aria-live="polite">
          <div class="card people">
            <svg class="icon" viewBox="0 0 32 32" aria-hidden="true"><circle cx="11" cy="10" r="5" fill="#1565a8"/><path d="M2 27c0-5.5 4-9 9-9s9 3.5 9 9z" fill="#1565a8"/><circle cx="23" cy="12" r="4" fill="#1b7f4b"/><path d="M17 27c0-4 2.6-7 6-7s7 3 7 7z" fill="#1b7f4b"/></svg>
            <div class="label">People taking part</div>
            <div class="value" id="out-people"></div>
            <div class="sub" id="out-summary"></div>
          </div>
          <div class="card energy">
            <svg class="icon" viewBox="0 0 32 32" aria-hidden="true"><path d="M18 2L6 18h8l-2 12 12-17h-8z" fill="#e6a700" stroke="#b37f00" stroke-width="1.2" stroke-linejoin="round"/></svg>
            <div class="label">Annual electricity saved</div>
            <div class="value" id="out-kwh"></div>
            <div class="sub">kWh per year</div>
          </div>
          <div class="card co2">
            <svg class="icon" viewBox="0 0 32 32" aria-hidden="true"><path d="M9 22a6 6 0 0 1 .5-12 8 8 0 0 1 15 2.5A5 5 0 0 1 24 22z" fill="#1b7f4b"/><text x="16" y="19.5" text-anchor="middle" font-size="8.5" font-weight="700" fill="#fff" font-family="Arial">CO₂</text></svg>
            <div class="label">CO₂ emissions avoided</div>
            <div class="value" id="out-tonnes"></div>
            <div class="sub">tonnes per year</div>
          </div>
        </div>

        <div class="secondary">
          <div class="tile"><div class="label">CO₂ emissions avoided</div><div class="value" id="out-kg"></div><div class="label">kg per year</div></div>
          <div class="tile"><div class="label">Saved per person per year</div><div class="value" id="out-perperson"></div><div class="label">kWh per person</div></div>
        </div>

        <div class="chart-box">
          <h3>Electricity saved vs. CO₂ avoided</h3>
          <p class="note">Each bar is scaled relative to the larger of the two values, so you can compare their size at a glance.</p>
          <div class="bars">
            <div class="bar-row">
              <div class="bar-head"><span>Electricity saved</span><span id="chart-kwh-label"></span></div>
              <div class="track"><div class="bar kwh" id="bar-kwh"></div></div>
            </div>
            <div class="bar-row">
              <div class="bar-head"><span>CO₂ emissions avoided</span><span id="chart-kg-label"></span></div>
              <div class="track"><div class="bar kg" id="bar-kg"></div></div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>

  <!-- ================= EXPLANATION ================= -->
  <section class="explain">
    <div class="message">
      One person’s electricity savings may seem small. However, when hundreds of people make similar changes consistently, the combined impact can become significant.
    </div>
    <div class="disclaimer">
      <strong>Disclaimer:</strong> These results are illustrative estimates, not guaranteed real-world reductions. Actual savings depend on the electricity genuinely saved, the time of use, and the electricity generation mix. The 0.45 kg CO₂/kWh factor is an assumed example and should be verified against the relevant Japanese electricity emissions data.
    </div>
  </section>
</div>

<script>
(function () {
  "use strict";

  // Default values, plus allowed ranges and display decimals for each input.
  const CONFIG = {
    people: { def: 500,  min: 10, max: 1000, step: 1,    dec: 0 },
    watts:  { def: 20,   min: 5,  max: 60,   step: 1,    dec: 0 },
    hours:  { def: 3,    min: 1,  max: 12,   step: 1,    dec: 0 },
    days:   { def: 365,  min: 30, max: 365,  step: 1,    dec: 0 },
    factor: { def: 0.45, min: 0.1, max: 1.0, step: 0.01, dec: 2 }
  };

  // Start with every value at its default so the first render never sees undefined
  const state = {};
  Object.keys(CONFIG).forEach((k) => { state[k] = CONFIG[k].def; });
  const sliders = {}, boxes = {};

  // Number formatting with thousands separators
  function fmt(n, decimals) {
    return n.toLocaleString("en-US", { minimumFractionDigits: decimals, maximumFractionDigits: decimals });
  }
  const $ = (id) => document.getElementById(id);

  // Keep a value inside its allowed range
  function clamp(key, v) {
    const c = CONFIG[key];
    if (isNaN(v)) return c.def;
    return Math.min(c.max, Math.max(c.min, v));
  }

  // ---------- Core calculations ----------
  function calculate(s) {
    // Annual electricity saved (kWh) = people × watts × hours/day × days/year ÷ 1,000
    // (watt-hours → kilowatt-hours requires dividing by 1,000)
    const kwh = (s.people * s.watts * s.hours * s.days) / 1000;

    // CO₂ avoided (kg) = annual kWh saved × emission factor (kg CO₂ per kWh)
    const kg = kwh * s.factor;

    // CO₂ avoided (tonnes) = kg ÷ 1,000
    const tonnes = kg / 1000;

    // Per-person share of the yearly electricity saving
    const perPerson = kwh / s.people;

    return { kwh, kg, tonnes, perPerson };
  }

  // ---------- Rendering ----------
  function render() {
    const r = calculate(state);

    $("out-people").textContent = fmt(state.people, 0);
    $("out-summary").textContent = fmt(state.watts, 0) + " W × " + fmt(state.hours, 0) + " h × " + fmt(state.days, 0) + " days";
    $("out-kwh").textContent = fmt(r.kwh, r.kwh % 1 === 0 ? 0 : 1);
    $("out-tonnes").textContent = fmt(r.tonnes, 2);
    $("out-kg").textContent = fmt(r.kg, 1);
    $("out-perperson").textContent = fmt(r.perPerson, 1);

    // Bar chart: both bars scaled to the larger of the two values
    const max = Math.max(r.kwh, r.kg, 1);
    $("bar-kwh").style.width = (r.kwh / max * 100) + "%";
    $("bar-kg").style.width = (r.kg / max * 100) + "%";
    $("chart-kwh-label").textContent = fmt(r.kwh, 1) + " kWh";
    $("chart-kg-label").textContent = fmt(r.kg, 1) + " kg CO₂";

    // Slider fill colour
    Object.keys(CONFIG).forEach((k) => {
      const c = CONFIG[k];
      sliders[k].style.setProperty("--fill", ((state[k] - c.min) / (c.max - c.min) * 100) + "%");
    });
  }

  // Update one value everywhere (slider, number box, state)
  function setValue(key, v, fromBox) {
    state[key] = v;
    sliders[key].value = v;
    if (!fromBox) boxes[key].value = CONFIG[key].dec ? v.toFixed(CONFIG[key].dec) : v;
    render();
  }

  // ---------- Setup ----------
  Object.keys(CONFIG).forEach((key) => {
    const c = CONFIG[key];
    sliders[key] = document.querySelector('input[type=range][data-key="' + key + '"]');
    boxes[key]   = document.querySelector('input[type=number][data-key="' + key + '"]');
    [sliders[key], boxes[key]].forEach((el) => { el.min = c.min; el.max = c.max; el.step = c.step; });

    sliders[key].addEventListener("input", () => setValue(key, parseFloat(sliders[key].value), false));

    // Typing: update live when valid; snap into range when the box loses focus
    boxes[key].addEventListener("input", () => {
      const v = parseFloat(boxes[key].value);
      if (!isNaN(v) && v >= c.min && v <= c.max) setValue(key, v, true);
    });
    boxes[key].addEventListener("change", () => setValue(key, clamp(key, parseFloat(boxes[key].value)), false));
  });

  function resetAll() {
    Object.keys(CONFIG).forEach((key) => setValue(key, CONFIG[key].def, false));
  }
  $("reset").addEventListener("click", resetAll);

  resetAll(); // initial values: 500 people, 20 W, 3 h, 365 days, 0.45 kg/kWh
})();
</script>
</body>
</html>
