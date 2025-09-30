[nefra_slow_typing.html](https://github.com/user-attachments/files/22618451/nefra_slow_typing.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Nefra — AI Perfume Muse (Slow Typing)</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <style>
    .bubble p { margin: 0.25rem 0; }
    .bubble ul { margin: 0.25rem 0 0.25rem 1rem; list-style: disc; }
    .rec-title{ font-weight:600; }
    .muted{ color:#6b7280; }
  </style>
</head>
<body class="bg-gradient-to-b from-[#0b0b0c] to-[#161618] text-gray-900">
  <div id="root"></div>

  <script type="text/babel">
    const { useEffect, useRef, useState } = React;

    const SCRIPTS = {
      romantic: {
        label: "Romantic Mood",
        turns: [
          { user: "I want something romantic.", nefra: "Romance can whisper close to the skin or bloom into the room. Do you want something <b>intimate</b> or more <b>expressive</b>?" },
          { user: "Intimate.", nefra: "I’ll keep projection soft and textures creamy. Three shades of romance—each with price, performance, seasonality, gender positioning, and layering:" },
          { user: "", nefra: `<span class="rec-title">1) Maison Francis Kurkdjian À la Rose (EDP)</span>
<ul>
  <li><b>Price:</b> ~€200 (70ml)</li>
  <li><b>Performance:</b> 6–7h, soft sillage (skin-close)</li>
  <li><b>Seasonality:</b> Spring / Summer evenings</li>
  <li><b>Gender:</b> Marketed feminine, <i>truly unisex in wear</i></li>
  <li><b>Layering:</b> Rose lotion or Amyris body oil to extend the glow</li>
</ul>
<p class="muted"><i>Feels like silk sheets at dawn, dew on rose petals—tender, luminous, elegant.</i></p>` },
          { user: "", nefra: `<span class="rec-title">2) BDK Parfums Gris Charnel (EDP)</span>
<ul>
  <li><b>Price:</b> ~€190 (100ml)</li>
  <li><b>Performance:</b> 7–8h, discreet/moderate</li>
  <li><b>Seasonality:</b> Autumn / Spring dinners</li>
  <li><b>Gender:</b> Unisex</li>
  <li><b>Layering:</b> Vanilla body cream to boost sandalwood creaminess</li>
</ul>
<p class="muted"><i>Fig skin and warm tea in a dim café—modern, sensual, slightly playful.</i></p>` },
          { user: "", nefra: `<span class="rec-title">3) Chanel Coromandel (Les Exclusifs, EDP)</span>
<ul>
  <li><b>Price:</b> ~€295 (75ml)</li>
  <li><b>Performance:</b> 8–10h, moderate (room-halo if oversprayed)</li>
  <li><b>Seasonality:</b> Autumn / Winter nights</li>
  <li><b>Gender:</b> Marketed feminine, commonly worn by men (unisex)</li>
  <li><b>Layering:</b> Amber/vanilla cream to soften patchouli</li>
</ul>
<p class="muted"><i>Velvet curtains and lacquered wood—patchouli smoothed by vanilla/benzoin; a cashmere shawl for the soul.</i></p>` },
          { user: "Which is best for a dinner date?", nefra: "For <b>tender intimacy</b> choose Gris Charnel. For <b>elegant delicacy</b> choose À la Rose. If you want to be <b>remembered</b>, Coromandel wraps you in quiet opulence." }
        ]
      }
      // (other scenarios omitted here for brevity, same as before)
    };

    const uid = () => Math.random().toString(36).slice(2);

    function Bubble({ role, text }) {
      const isUser = role === "user";
      return (
        <div className={"w-full flex " + (isUser ? "justify-end" : "justify-start")}>
          <div className={"bubble max-w-[80%] rounded-2xl px-4 py-3 shadow " + (isUser ? "bg-gray-900 text-white rounded-br-md" : "bg-[#fcfbf7] text-gray-900 border border-[#ece7df] rounded-bl-md")}>
            <div className="leading-relaxed whitespace-pre-line" dangerouslySetInnerHTML={{__html: text}}></div>
            <div className="mt-1 text-[10px] opacity-60 text-right">{isUser ? "You" : "Nefra"}</div>
          </div>
        </div>
      );
    }

    function NefraPrototype() {
      const [messages, setMessages] = useState([{ id: uid(), role: "nefra", text: "Hello, I’m Nefra — your AI perfume muse. Tap a scenario below and I’ll guide you with boutique‑style advice." }]);
      const [isPlaying, setIsPlaying] = useState(false);
      const logRef = useRef(null);

      useEffect(() => { logRef.current?.scrollTo({ top: logRef.current.scrollHeight, behavior: "smooth" }); }, [messages]);

      const sleep = (ms) => new Promise((r) => setTimeout(r, ms));

      const typewriterAppend = async (full) => {
        const id = uid();
        let current = "";
        setMessages((m) => [...m, { id, role: "nefra", text: current }]);
        for (const ch of full) {
          current += ch;
          setMessages((m) => m.map((msg) => (msg.id === id ? { ...msg, text: current } : msg)));
          await sleep(35); // slower typing
        }
      };

      const playScript = async (key) => {
        if (isPlaying) return;
        setIsPlaying(true);
        const seq = SCRIPTS[key].turns;
        for (const t of seq) {
          if (t.user) setMessages((m) => [...m, { id: uid(), role: "user", text: t.user }]);
          await sleep(800); // pause after user
          await typewriterAppend(t.nefra);
          await sleep(600); // pause before next turn
        }
        setIsPlaying(false);
      };

      const reset = () => setMessages([{ id: uid(), role: "nefra", text: "Hello, I’m Nefra — your AI perfume muse. Tap a scenario below and I’ll guide you with boutique‑style advice." }]);

      return (
        <div className="min-h-screen w-full p-6">
          <div className="mx-auto max-w-3xl">
            <div className="mb-4 flex items-center justify-between">
              <div className="flex items-center gap-3">
                <div className="h-10 w-10 rounded-2xl bg-gray-900 text-white grid place-items-center font-semibold">N</div>
                <div>
                  <h1 className="text-xl font-semibold text-white">Nefra — AI Perfume Muse</h1>
                  <p className="text-sm text-gray-300">Now with slower letter-by-letter typing & pauses</p>
                </div>
              </div>
              <button onClick={reset} className="px-3 py-2 rounded-xl bg-white border border-gray-200 text-sm shadow hover:shadow-md">Reset</button>
            </div>

            <div className="rounded-2xl bg-white shadow-lg border border-[#ece7df] overflow-hidden">
              <div ref={logRef} className="h-[60vh] overflow-y-auto p-4 space-y-3 bg-white">
                {messages.map((m) => (<Bubble key={m.id} role={m.role} text={m.text} />))}
              </div>
              <div className="border-t border-[#ece7df] p-4">
                <div className="grid grid-cols-2 md:grid-cols-4 gap-2">
                  {Object.entries(SCRIPTS).map(([key, cfg]) => (
                    <button key={key} onClick={() => playScript(key)} disabled={isPlaying}
                      className={"rounded-xl px-3 py-2 text-sm shadow " + (isPlaying ? "bg-gray-100 text-gray-400 border border-gray-200" : "bg-gray-900 text-white hover:shadow-md")}>
                      {cfg.label}
                    </button>
                  ))}
                </div>
              </div>
            </div>
          </div>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById("root"));
    root.render(<NefraPrototype />);
  </script>
</body>
</html>
