[index.html](https://github.com/user-attachments/files/22607933/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Nefra — AI Perfume Muse (Prototype)</title>
  <!-- Tailwind (CDN) -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- React 18 UMD + Babel -->
  <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body class="bg-neutral-200">
  <div id="root"></div>

  <script type="text/babel">
    const { useEffect, useRef, useState } = React;

    // --- Pre-scripted demo dialogues (edit freely)
    const SCRIPTS = {
      romantic: {
        label: "Romantic Mood",
        turns: [
          {
            user: "I want something romantic.",
            nefra:
              "Romance often hides in rose and vanilla, softened by musk. This blend lasts 7–8 hours with an intimate sillage — close, personal, almost secretive. Would you like a bolder variation with stronger projection?",
          },
        ],
      },
      warmNight: {
        label: "Warm Night",
        turns: [
          {
            user: "I want something warm for the night.",
            nefra:
              "Imagine saffron at dusk melting into velvet rose, then oud that lingers for 8–10 hours. It projects with quiet strength — elegant, never overpowering. Want me to compare it with a lighter amber option?",
          },
        ],
      },
      freshWork: {
        label: "Fresh for Work",
        turns: [
          {
            user: "I need something fresh for the office.",
            nefra:
              "Citrus and neroli open like crisp morning air, lasting 5–6 hours with a subtle projection — professional without intrusion. Shall I suggest an alternative with stronger staying power?",
          },
        ],
      },
      discontinued: {
        label: "Discontinued Alternative",
        turns: [
          {
            user: "I miss my Tom Ford Plum Japonais, but it’s discontinued.",
            nefra:
              "Plum Japonais was opulent — plum, cinnamon and amber, ~8–10 hours. A close alternative is Serge Lutens Fille en Aiguilles: spicy‑plum warmth with ~7–8 hours. Want two more options in this style available in Germany?",
          },
        ],
      },
    };

    // --- Helpers
    const uid = () => Math.random().toString(36).slice(2);

    function Bubble({ role, text }) {
      const isUser = role === "user";
      return (
        <div className={"w-full flex " + (isUser ? "justify-end" : "justify-start")}>
          <div
            className={
              "max-w-[80%] rounded-2xl px-4 py-3 shadow " +
              (isUser
                ? "bg-gray-900 text-white rounded-br-md"
                : "bg-white text-gray-900 border border-gray-200 rounded-bl-md")
            }
          >
            <p className="leading-relaxed">{text}</p>
            <div className="mt-1 text-[10px] opacity-60 text-right">
              {isUser ? "You" : "Nefra"}
            </div>
          </div>
        </div>
      );
    }

    function NefraPrototype() {
      const [messages, setMessages] = useState([
        { id: uid(), role: "nefra", text: "Hello, I’m Nefra — your AI perfume muse. Tap a scenario below and I’ll guide you." },
      ]);
      const [isPlaying, setIsPlaying] = useState(false);
      const logRef = useRef(null);

      // Auto-scroll on new messages
      useEffect(() => {
        if (logRef.current) {
          logRef.current.scrollTo({ top: logRef.current.scrollHeight, behavior: "smooth" });
        }
      }, [messages]);

      const sleep = (ms) => new Promise((r) => setTimeout(r, ms));

      const typewriterAppend = async (full) => {
        const id = uid();
        let current = "";
        setMessages((m) => [...m, { id, role: "nefra", text: current }]);
        for (const ch of full) {
          current += ch;
          setMessages((m) => m.map((msg) => (msg.id === id ? { ...msg, text: current } : msg)));
          await sleep(10);
        }
      };

      const playScript = async (key) => {
        if (isPlaying) return;
        setIsPlaying(true);
        const seq = SCRIPTS[key].turns;
        for (const t of seq) {
          setMessages((m) => [...m, { id: uid(), role: "user", text: t.user }]);
          await sleep(400);
          await typewriterAppend(t.nefra);
          await sleep(300);
        }
        setIsPlaying(false);
      };

      const reset = () => {
        setMessages([{ id: uid(), role: "nefra", text: "Hello, I’m Nefra — your AI perfume muse. Tap a scenario below and I’ll guide you." }]);
      };

      return (
        <div className="min-h-screen w-full bg-gradient-to-b from-neutral-100 to-neutral-200 p-6">
          <div className="mx-auto max-w-3xl">
            {/* Header */}
            <div className="mb-4 flex items-center justify-between">
              <div className="flex items-center gap-3">
                <div className="h-10 w-10 rounded-2xl bg-gray-900 text-white grid place-items-center font-semibold">N</div>
                <div>
                  <h1 className="text-xl font-semibold">Nefra — AI Perfume Muse</h1>
                  <p className="text-sm text-gray-600">Clickable prototype with four demo dialogues</p>
                </div>
              </div>
              <button onClick={reset} className="px-3 py-2 rounded-xl bg-white border border-gray-200 text-sm shadow hover:shadow-md">Reset</button>
            </div>

            {/* Chat Card */}
            <div className="rounded-2xl bg-white shadow-lg border border-gray-200 overflow-hidden">
              <div ref={logRef} className="h-[56vh] overflow-y-auto p-4 space-y-3 bg-white">
                {messages.map((m) => (
                  <Bubble key={m.id} role={m.role} text={m.text} />
                ))}
              </div>

              {/* Controls */}
              <div className="border-t border-gray-200 p-4">
                <div className="grid grid-cols-2 md:grid-cols-4 gap-2">
                  {Object.entries(SCRIPTS).map(([key, cfg]) => (
                    <button
                      key={key}
                      onClick={() => playScript(key)}
                      disabled={isPlaying}
                      className={
                        "rounded-xl px-3 py-2 text-sm shadow " +
                        (isPlaying ? "bg-gray-100 text-gray-400 border border-gray-200" : "bg-gray-900 text-white hover:shadow-md")
                      }
                    >
                      {cfg.label}
                    </button>
                  ))}
                </div>
                <div className="mt-3 text-xs text-gray-500">
                  Tip: Use this as a thesis demo. You can adapt copy, add products, or wire it to a real model later.
                </div>
              </div>
            </div>

            {/* How this can evolve */}
            <div className="mt-6 grid gap-3 md:grid-cols-3">
              <div className="rounded-2xl p-4 bg-white border border-gray-200 shadow">
                <h3 className="font-semibold mb-1">Phase 1 · Demo</h3>
                <p className="text-sm text-gray-600">Static scripted replies (this prototype). Show tone, pacing, and UX.</p>
              </div>
              <div className="rounded-2xl p-4 bg-white border border-gray-200 shadow">
                <h3 className="font-semibold mb-1">Phase 2 · Smart Rules</h3>
                <p className="text-sm text-gray-600">Add simple intent rules (mood/occasion/notes) + a small JSON of perfumes.</p>
              </div>
              <div className="rounded-2xl p-4 bg-white border border-gray-200 shadow">
                <h3 className="font-semibold mb-1">Phase 3 · Live Data</h3>
                <p className="text-sm text-gray-600">Connect to a real LLM + curated database (e.g., brand catalogue, longevity fields).</p>
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
