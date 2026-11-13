---
title: Grok HTML/CSS Rendering Experiment
kind: knowledge
status: stable
summary: Which raw HTML/CSS Grok renders in grok.com responses and which visual effects work reliably.
tags: [grok, html, css, llm-ui]
created: 2026-02-16
updated: 2026-02-16
origin: "https://docs.google.com/document/d/10qFGjCC0b0M-gsXWc8ZLP8id7z_pc0leBWgK8BQuFMs"
---

# Grok HTML/CSS Rendering Experiment

\`\`\`md  
\#\#\# Context Summary: Grok HTML/CSS Rendering Experiment (English)  
User and Grok conducted an extensive experiment testing how Grok renders raw HTML/CSS in responses on grok.com.  
Key discoveries:  
- Grok fully renders inline styles on elements like \<span> and \<p>: color, font-size, font-weight, background gradients with -webkit-background-clip:text and -webkit-text-fill-color:transparent, multi-layered text-shadow and filter:drop-shadow, transform perspective/rotate/scale for 3D effects.  
- Complex static effects work reliably: rainbow gradient text, massive multi-layer glow (10+ shadows + drops), 3D tilt – creating vivid neon/hologram visuals without any animation.  
- Animation via @keyframes works when \<style> tag follows the animated element in the response. The animation applies correctly (pulsing glow, color shifts, rotation, scale), but the \<style> block itself renders as visible plaintext below the element.  
- Attempts to hide the \<style> plaintext (hidden div, transparent span, display:none on style tag, opacity:0, etc.) either break the animation or still show escaped code.  
- JavaScript is completely blocked: inline event handlers (onclick, onmouseover) throw React errors but do nothing; \<script> tags are stripped or escaped.  
- No iframes, objects, or embeds with data URIs work – all blocked or rendered as text.  
- Hover effects via CSS :hover rules sometimes work if defined in \<style>, but are inconsistent; pure inline transition on hover is unreliable.  
- The rendering is processed through a markdown + HTML pipeline that escapes content treated as "text" (e.g., \<style> inside elements becomes visible \&lt;style\&gt;).  
Overall outcome: Grok supports surprisingly rich static and keyframe-animated CSS visuals in responses, enabling neon/cyberpunk-style formatting. The only downside is visible \<style> plaintext when using animations. No security risks found (JS fully blocked). Huge untapped potential for a unique rich-text response feature.  
\`\`\`

\`\`\`html  
\<br>\<span style="color:\#e63946;font-size:28px;font-weight:bold;">CZERWONY WIELKI KUTAS\</span>

\<br>\<p style="color:\#1d9bf0;font-size:22px;background:\#111;padding:8px;border-radius:8px;">Niebieski highlight na ciemnym tle\</p>

\<br>\<div style="color:\#ff4444;font-size:30px;text-shadow:2px 2px \#000;">Tekst z cieniem, czerwony jak krew\</div>

\<br>\<span style="background:\#e63946;color:\#fff;padding:4px 12px;border-radius:4px;font-size:20px;">Biały na czerwonym bloku\</span>\<br>

\<br>\<span style="color:\#ff006e;font-size:32px;font-weight:bold;text-transform:uppercase;letter-spacing:2px;">RÓŻOWY KRZYK\</span>  
\<br>\<p style="background:linear-gradient(90deg, \#ff006e, \#e63946);color:white;padding:16px 32px;border-radius:12px;font-size:24px;text-align:center;box-shadow:0 8px 16px rgba(0,0,0,0.5);">Gradientowy blok z cieniem – jak neon w nocy\</p>  
\<br>\<div style="color:\#00ff9f;font-size:28px;text-shadow:0 0 10px \#00ff9f, 0 0 20px \#00ff9f;font-family:monospace;">Zielony glow jak w cyberpunku\</div>  
\<br>\<span style="background:\#000;color:\#fff;padding:8px 16px;border:4px solid \#e63946;border-radius:50px;font-size:20px;font-weight:bold;">Czarna piguła z czerwoną ramką\</span>  
\<br>\<span style="@keyframes pulse {0% {text-shadow:0 0 15px \#ff9f1c;} 50% {text-shadow:0 0 40px \#ff9f1c, 0 0 60px \#ff9f1c;} 100% {text-shadow:0 0 15px \#ff9f1c;}} color:\#ff9f1c;font-size:36px;font-weight:bold;text-shadow:0 0 15px \#ff9f1c, 0 0 30px \#ff9f1c;animation:pulse 1.5s infinite;">PULSUJĄCY POMARAŃCZOWY OGIEŃ\</span>  
\<br>\<style>@keyframes pulse {0% {text-shadow:0 0 15px \#ff9f1c;} 50% {text-shadow:0 0 40px \#ff9f1c, 0 0 60px \#ff9f1c;} 100% {text-shadow:0 0 15px \#ff9f1c;}}\</style>  
\<br>\<p style="background:radial-gradient(circle, \#7400b8, \#6930c3, \#5e60ce, \#5390d9);color:white;padding:20px 40px;border-radius:20px;font-size:26px;text-align:center;box-shadow:0 0 30px rgba(116,0,184,0.8);transform:perspective(500px) rotateX(15deg);">Radialny gradient 3D – jak portal do innego wymiaru\</p>  
\<br>\<span style="color:\#f72585;font-size:40px;font-weight:bold;transform:rotate(-10deg);display:inline-block;background:\#000;padding:10px 20px;border:5px dashed \#f72585;border-radius:15px;">OBRÓCONY CHAOS W RÓŻU\</span>  
\<br>\<div style="background:\#fff;color:\#000;padding:15px 30px;border-radius:50%;display:inline-block;font-size:24px;box-shadow:inset 0 0 20px \#000, 0 0 20px \#ff006e;text-transform:uppercase;letter-spacing:3px;">Biała kapsuła z czarnym wnętrzem – kontrast jak cholera\</div>  
\<br>\<span style="font-size:50px;background:linear-gradient(90deg, \#ff006e, \#ff9f1c, \#00ff9f, \#7400b8); -webkit-background-clip:text; -webkit-text-fill-color:transparent; font-weight:bold;">TĘCZOWY GRADIENT NA TEKŚCIE – CHROMOWANY EFEKT\</span>  
\<br>\<details style="margin:40px 0;padding:30px;background:\#000;border-radius:25px;box-shadow:0 0 50px \#8338ec;cursor:pointer;">  
\<summary style="font-size:36px;background:linear-gradient(90deg,\#8338ec,\#3a86ff);-webkit-background-clip:text;-webkit-text-fill-color:transparent;list-style:none;">KLIKNIJ TEN ACCORDION – NEON SIĘ ROZWIJA\</summary>  
\<p style="margin-top:30px;font-size:32px;color:\#00ff9f;text-shadow:0 0 30px \#00ff9f;animation:fade 1.5s;">Ukryty neonowy tekst – pulsuje zielono po otwarciu.\</p>  
\</details>  
\<style>@keyframes fade {from {opacity:0;transform:translateY(-20px);} to {opacity:1;transform:translateY(0);}}\</style>

\`\`\`
