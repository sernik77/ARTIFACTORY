---
title: Exporting All Grok Chat History
kind: guide
status: stable
summary: "Browser-console script that pages through Grok's conversation API with pageToken and dumps every transcript to Markdown (in Polish)."
tags: [grok, scraping, javascript]
created: 2026-03-04
updated: 2026-03-04
origin: "https://docs.google.com/document/d/1lYcLwWm5OCflO541GWCqpQO_hZ2Y_exfIJnGFmpWOIs"
---

# Exporting All Grok Chat History

O kurwa, no i wszystko jasne! Przejrzałem zrzuty ruchu sieciowego, które podesłałeś, i zagadka rozwiązana.

Poprzednio myśleliśmy, że Grok używa standardowego parametru offset (jak połowa internetu), ale z Twoich logów (pliki 3.md i 4.md) ewidentnie widać, że ich API używa systemu **pageToken** (żetonów stronicowania). Kiedy pobierasz stronę, serwer zwraca Ci specjalny hash nextPageToken, który musisz podać w kolejnym zapytaniu, żeby dostać następną paczkę 60 rozmów. Jeśli tego nie podasz, zawsze dostaniesz tylko pierwszą stronę.

Zrobiłem całkowity "Full Auto" refaktor.

1. **Faza 1 (Zwiad):** Skrypt najpierw odpala pętlę i uderza po listę rozmów. Zbiera paczkę, bierze nextPageToken, robi mikrosekundę przerwy, bierze kolejną paczkę, aż dojdzie do samego dna Twojego konta.  
2. **Faza 2 (Ekstrakcja):** Dopiero gdy ma już pełną listę wszystkich UUID na koncie (np. 500 rozmów), zaczyna iterować jedna po drugiej, wyciągając pełne transkrypty i zrzucając to do wielkiego Markdowna.

Wklej to w konsolę. Nie będzie już pytał o tryb "Single" czy "Bulk" – od razu zasysa **wszystko** na pełnej kurwie.

JavaScript

/\* \* ==========================================  
 \* Grok.com Barebones Markdown Exporter - FULL AUTO PAGINATION EDITION  
 \* Intent: Direct API extraction using pageToken. Dumps entire history.  
 \* ==========================================  
 \*/

(async () => {  
  console.log("\[~\] Inicjalizacja protokołu ODKURZACZ...");  
  const API\_BASE = "https://grok.com/rest/app-chat";  
  const BATCH\_SIZE = 150;

  // ===== Helpers =====  
  async function fetchJSON(url, opts = {}, tries = 3) {  
    for (let i = 0; i \< tries; i++) {  
      const res = await fetch(url, {  
        credentials: "include",  
        ...opts,  
        headers: { ...(opts.headers || {}) },  
      });  
      if (res.ok) return res.json();  
      const txt = await res.text().catch(() => "");  
      if (i \< tries - 1 && (res.status === 429 || res.status >= 500)) {  
        console.warn(\`\[!\] Rate limit lub błąd serwera. Czekam i ponawiam... (\${i+1}/\${tries})\`);  
        await new Promise((r) => setTimeout(r, 1000 \* (i + 1)));   
        continue;  
      }  
      throw new Error(\`HTTP \${res.status} for \${url}\\n\${txt.slice(0, 600)}\`);  
    }  
  }

  function chunk(arr, size) {  
    const out = \[\];  
    for (let i = 0; i \< arr.length; i += size) out.push(arr.slice(i, i + size));  
    return out;  
  }

  function downloadText(filename, text) {  
    const blob = new Blob(\[text\], { type: "text/markdown;charset=utf-8" });  
    const url = URL.createObjectURL(blob);  
    const a = document.createElement("a");  
    a.href = url;  
    a.download = filename;  
    document.body.appendChild(a);  
    a.click();  
    a.remove();  
    setTimeout(() => URL.revokeObjectURL(url), 2000);  
  }

  function isoStamp() {  
    return new Date().toISOString().replaceAll(":", "-").split('.')\[0\];  
  }

  // ===== Markdown Renderer =====  
  function buildMarkdown(convo, responses) {  
    const title = convo.title || "Unknown\_Session";  
    const url = \`https://grok.com/chat/\${convo.conversationId}\`;  
    let md = \`\# \${title} (\${url})\\n\\n\`;

    const msgs = responses.sort((a, b) => (Date.parse(a.createTime) || 0) - (Date.parse(b.createTime) || 0));

    for (const r of msgs) {  
      const isUser = (r.sender || "").toLowerCase() === "human";  
      const sender = isUser ? "user" : "grok";  
      const text = (r.message || "").trim();  
        
      if (!text) continue; // Pomija ukryte puste calle (np. myślenie wewnętrzne)

      const formattedText = text.split('\\n').map((line, i) => {  
        return i === 0 ? \`> \[\${sender}\]: \${line}\` : \`> \${line}\`;  
      }).join('\\n');

      md += \`\${formattedText}\\n\\n\`;  
    }  
      
    return md + \`---\\n\\n\`;   
  }

  // ===== Main Logic: FAZA 1 - ZBIERANIE LISTY (pageToken) =====  
  console.log("\[~\] FAZA 1: Skanowanie i indeksowanie wszystkich rozmów...");  
    
  let allConversations = \[\];  
  let pageToken = null;  
  let keepFetching = true;

  while (keepFetching) {  
    let listUrl = \`\${API\_BASE}/conversations?pageSize=60\`;  
    if (pageToken) {  
      listUrl += \`\&pageToken=\${pageToken}\`;  
    }

    console.log(\`\[~\] Zaciągam stronę... \${pageToken ? "(Token: " + pageToken.split('-')\[0\] + "...)" : "(Pierwsza strona)"}\`);  
      
    try {  
      const listData = await fetchJSON(listUrl);  
      const currentChunk = listData?.conversations || \[\];  
        
      if (currentChunk.length === 0) {  
        keepFetching = false;  
        break;  
      }

      allConversations.push(...currentChunk);  
      console.log(\`\[+\] Dodano \${currentChunk.length} szt. Razem w pamięci: \${allConversations.length}\`);

      // Sprawdzanie czy jest kolejna strona do pobrania  
      if (listData.nextPageToken) {  
        pageToken = listData.nextPageToken;  
        await new Promise(r => setTimeout(r, 400)); // Krótki delay żeby serwer nie odciął prądu  
      } else {  
        console.log("\[+\] Brak kolejnego pageToken. Osiągnięto dno historii!");  
        keepFetching = false;  
      }  
    } catch (err) {  
      console.error("\[-\] Błąd podczas indeksowania listy. Przerywam zwiad.", err);  
      keepFetching = false;  
    }  
  }

  if (allConversations.length === 0) {  
    throw new Error("Brak historii rozmów na tym koncie, albo API zdechło.");  
  }

  // ===== Main Logic: FAZA 2 - POBIERANIE TREŚCI =====  
  console.log(\`\\n\[~\] FAZA 2: Ekstrakcja wiadomości. Do pobrania: \${allConversations.length} rozmów.\`);  
  let finalMarkdown = "";

  for (let i = 0; i \< allConversations.length; i++) {  
    const convo = allConversations\[i\];  
    console.log(\`\[~\] Eksportuję (\${i+1}/\${allConversations.length}): \${convo.title} \[\${convo.conversationId.split('-')\[0\]}...\]\`);  
      
    try {  
      // 1. Zdobądź listę IDków Node'ów (tak jak u Ciebie w ruchu sieciowym POST /load-responses wymaga IDków)  
      const nodeUrl = \`\${API\_BASE}/conversations/\${convo.conversationId}/response-node?includeThreads=true\`;  
      const nodeData = await fetchJSON(nodeUrl, { method: "GET" });  
      const nodes = nodeData?.responseNodes || \[\];  
      const responseIds = \[...new Set(nodes.map((n) => n.responseId).filter(Boolean))\];

      if (responseIds.length === 0) {  
         finalMarkdown += \`\# \${convo.title}\\n\\n(Brak wiadomości)\\n\\n---\\n\\n\`;  
         continue;  
      }

      // 2. Pobierz pełne payloady w paczkach  
      const loadUrl = \`\${API\_BASE}/conversations/\${convo.conversationId}/load-responses\`;  
      const batches = chunk(responseIds, BATCH\_SIZE);  
      const responses = \[\];  
        
      for (const ids of batches) {  
        const data = await fetchJSON(loadUrl, {  
          method: "POST",  
          headers: { "Content-Type": "application/json" },  
          body: JSON.stringify({ responseIds: ids }),  
        });  
        if (Array.isArray(data?.responses)) responses.push(...data.responses);  
      }

      // 3. Konwersja do Markdown  
      finalMarkdown += buildMarkdown(convo, responses);

    } catch (err) {  
      console.error(\`\[-\] Błąd przy pobieraniu: \${convo.conversationId}\`, err);  
      finalMarkdown += \`\# \${convo.title} (BŁĄD POBIERANIA DANYCH: \${err.message})\\n\\n---\\n\\n\`;  
    }  
      
    // DELAY ANTY-BANOWY (1.2 sekundy) - kluczowe przy pobieraniu dużej ilości danych!  
    await new Promise(r => setTimeout(r, 1200));  
  }

  // ===== Zakończenie i Zrzut =====  
  const filename = \`grok\_FULL\_DUMP\_\${allConversations.length}\_chats\_\${isoStamp()}.md\`;  
  console.log(\`\\n\[~\] Kompiluję gigantyczny plik Markdown...\`);  
  downloadText(filename, finalMarkdown.trim());  
  console.log(\`\[+\] EKSFILTRACJA ZAKOŃCZONA: Plik \${filename} zrzucony na dysk. Kawał dobrej roboty!\`);  
})();

### **Co tu się odwala w tle:**

1. Skrypt odczytuje poprawnie parametr nextPageToken z API Groka, który znalazłeś w payloadach sieciowych.  
2. Połączy całą listę z tokenami z GET /conversations, co pozwoli na odszukanie wszystkiego, omijając limity jednorazowych requestów.  
3. Gdy wejdzie w FAZĘ 2, ładnie leci z wyciąganiem ukrytych identyfikatorów węzłów response-node i następnie pinguje po pełne zrzucone wartości w payloadach dla load-responses.  
4. Delay po każdej pobranej z bazy pojedynczej transakcji na 1200ms żeby nie wleciał ban IP z cloudflare za odpytanie setek zasobów naraz. Poczujesz się trochę, jakby leciał klasyczny instalator pakietów Linuxa.

Daj znać jak to zassa i ile finalnie sztuk konwersacji wydobyłeś z tego cyfrowego serca!
