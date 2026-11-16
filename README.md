Prompt:
A simple,high-engagement, single-page clicker web app where the user pets a cute, minimalist cat to trigger delightful random reactions.
please create a website with these tech stack:
HTML5, CSS Tailwind, Vanilla Javascript

Visual Requirements (Cute, Fun, Modern):
The Cat: I need a central, large, cute, minimalist (vector or pixel art style) cat. it should look like it's floating or sitting on a soft surface. use tailwind's aspect-square to keep it centered.

Aesthetic (Soft Glassmorphism):
Background: A very soft, pastel gradient (e.g, #FFD1DC to #B5FFFC) that gives "Cotton-candy/PAstel" energy.
Containers: use bg-white/30, backdrop-blur-lg, rounded-full (like bubbles), and border-white/40 for interaction counters.
Typography: A playful, rounded sans-serif font (like Quicksand or Varela Round).

Features & Logic (Vanilla JS)
The "Pet" Trigger: When the user clicks (pets) the cat element, trigger a visual reaction.
The Reactions (Random): On click, generate a random number (1-100). Assign reactions based on these percentages:
Happy (40%):
Animation: The cat "jumps" slightly (animate-bounce).
Text Overlay: "YAY! 😊 (+5 Aura)"
Purring (30%):
Animation: A subtle animate-pulse on the whole cat element.

Sound: Play a soft "purr.mp3" loop for 2 seconds.
Sleeping (20%):
Animation: The cat scales down slightly (scale-90) and tilts (rotate-6).
Visual: Show "Zzz..." emoji fading in above it.
Zoomies (10%):
Animation: A violent, fast animate-wiggle (custom Tailwind animation) or a rapid translate-x.
Visuals: Speed-line emojis (💨) appear.
Sounds & Micro-interactions
Petting Sound: Every click should have a subtle, satisfying "pop" or "meow" sound.
Sound on Zoomies: A funny "zoom!" or "boing" sound.
The "Pet Counter": Keep track of total pets. Add a small text indicator below the cat (e.g., "Total Pets: 15 🐾").

Tech Stack:
HTML5, Tailwind CSS (via CDN), Vanilla JavaScript.
kiro, Please use useSound.js or standard HTML5 Audio() objects for the sounds.
kiro, Implement custom keyframes in the Tailwind config for the "wiggle" (Zoomies) and "Zzz fade-in" animations.