const yesBtn = document.getElementById("yesBtn");
const noBtn = document.getElementById("noBtn");
const arena = document.getElementById("arena");
const result = document.getElementById("result");
const confetti = document.getElementById("confetti");
const replayBtn = document.getElementById("replayBtn");
const copyBtn = document.getElementById("copyBtn");

let dodges = 0;

// Move "No" smoothly within arena (less random-chaos, more playful slide)
function slideNo() {
  const r = arena.getBoundingClientRect();
  const bw = noBtn.offsetWidth;
  const bh = noBtn.offsetHeight;

  const pad = 10;
  const maxX = Math.max(pad, r.width - bw - pad);
  const maxY = Math.max(pad, r.height - bh - pad);

  // biased movement: mostly sideways, a little up/down
  const x = pad + Math.random() * (maxX - pad);
  const y = pad + Math.random() * (maxY - pad);

  noBtn.style.transition = "left 140ms ease, top 140ms ease, transform 140ms ease";
  noBtn.style.left = `${x}px`;
  noBtn.style.top = `${y}px`;
  noBtn.style.transform = `rotate(${(Math.random()*8-4).toFixed(1)}deg)`;
}

function growYes() {
  dodges += 1;
  const scale = 1 + Math.min(dodges * 0.14, 1.2); // max ~2.2x
  yesBtn.style.transform = `translateY(-1px) scale(${scale})`;

  // after a few dodges, make it “obvious”
  if (dodges >= 6) {
    yesBtn.textContent = "Yes 💖 (easy choice)";
  }
}

function burst() {
  const rect = arena.getBoundingClientRect();
  const colors = ["#ff4da6", "#ff1f7a", "#14131a", "#ffffff"]; // still stylish, not identical
  const count = 120;

  for (let i = 0; i < count; i++) {
    const s = document.createElement("span");
    const x = Math.random() * rect.width;
    const y = Math.random() * rect.height;

    s.style.left = `${x}px`;
    s.style.top = `${y}px`;
    s.style.background = colors[Math.floor(Math.random() * colors.length)];
    s.style.animationDelay = `${Math.random() * 140}ms`;
    confetti.appendChild(s);

    setTimeout(() => s.remove(), 1100);
  }
}

function showResult() {
  result.classList.remove("hidden");
  burst();
  // disable arena buttons after success
  yesBtn.disabled = true;
  noBtn.disabled = true;
  yesBtn.style.filter = "saturate(1.1)";
}

function reset() {
  dodges = 0;
  result.classList.add("hidden");
  yesBtn.disabled = false;
  noBtn.disabled = false;
  yesBtn.textContent = "Yes 💖";
  yesBtn.style.transform = "translateY(0) scale(1)";
  noBtn.style.left = "60%";
  noBtn.style.top = "62px";
  noBtn.style.transform = "rotate(0deg)";
}

noBtn.addEventListener("mouseenter", () => { slideNo(); growYes(); });
noBtn.addEventListener("click",      () => { slideNo(); growYes(); });
yesBtn.addEventListener("click", showResult);
replayBtn.addEventListener("click", reset);

copyBtn.addEventListener("click", async () => {
  try {
    await navigator.clipboard.writeText(window.location.href);
    copyBtn.textContent = "Kopiert ✅";
    setTimeout(() => (copyBtn.textContent = "Link kopieren"), 900);
  } catch {
    alert("Kopieren ging nicht — URL bitte manuell kopieren.");
  }
});
