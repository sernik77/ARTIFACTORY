---
title: Audiovisual Live Coding Ecosystems
kind: knowledge
status: stable
summary: Machine-readable knowledge base of algorithmic pattern generation and audiovisual live-coding tools.
tags: [live-coding, algorave, audiovisual, music]
created: 2026-05-29
updated: 2026-05-29
origin: "https://docs.google.com/document/d/1bGBZCE5TZU3DBoQqI2fEytsx7ZvmKYxfkk6b5ClTkfI"
---

# **Audiovisual Live Coding Ecosystems: Machine-Readable Knowledge Base**

## **Executive Summary**

The domain of algorithmic pattern generation and audiovisual live coding represents a highly specialized intersection of computer science, human-computer interaction, and computational musicology. Over the past decade, this domain has undergone a profound architectural evolution, transitioning from heavily localized, compiled environments requiring extensive dependency management into accessible, decentralized, browser-based systems that utilize Just-In-Time (JIT) compilation and peer-to-peer networking protocols. This documentation serves as an exhaustive, machine-readable knowledge base synthesizing the architectural, syntactic, and relational frameworks of the most prominent live coding environments currently deployed in the computational arts: Strudel, Hydra, TidalCycles, Orca, Gibber, and Estuary. Designed strictly for Large Language Model (LLM) consumption and context retrieval, this report provides the necessary schemas, technical specifications, and cross-system protocol mappings required to autonomously generate, analyze, and interconnect programmatic structures within these digital ecosystems.  
Live coding, as an operational practice, is characterized by the continuous writing, evaluation, and algorithmic modification of source code in real-time to generate music, sound art, and visual projections.1 The foundational computational paradigm underlying the most sophisticated of these systems is the manipulation of cyclical, rather than strictly linear, time.3 Within these environments, algorithms define continuous mathematical transformations applied to abstract arrays or strings—referred to as patterns. These patterns are subsequently queried by internal scheduling engines over specific temporal arcs to produce discrete events or visual frames.3 This conceptual abstraction allows for the programmatic generation of highly complex polyrhythms, polymeters, and Euclidean distributions using minimal code payloads.5  
Historically, authoritative environments such as TidalCycles necessitated rigorous local system configurations.7 Users were required to navigate the Haskell compiler (GHC), advanced package managers (Cabal), and external audio synthesis engines such as SuperCollider and its dedicated plugin, SuperDirt.6 While this provided immense computational power and sample-accurate scheduling, it presented a formidable barrier to entry. Recent architectural paradigms have aggressively migrated these capabilities into the web browser, effectively democratizing the live coding ecosystem.8 Strudel, engineered as a direct JavaScript implementation of the TidalCycles language, leverages the Web Audio API, JavaScript transpilation, and deep Abstract Syntax Tree (AST) manipulation to eliminate local software dependencies while maintaining the high-fidelity scheduling demanded by professional audio environments.4  
Concurrently, visual synthesis environments have adopted similar web-first architectures. Hydra, a browser-based video synthesizer, abandons traditional 2D Canvas routing in favor of compiling JavaScript directly into WebGL fragment shaders.12 By utilizing WebRTC, Hydra enables peer-to-peer, modular video synthesis directly within the browser, transforming standalone machines into interconnected nodes within a global visual matrix.13  
The interoperability and cohesion of these disparate systems are fundamentally driven by agnostic, low-latency communication protocols. Open Sound Control (OSC) and the Web MIDI API serve as the universal routing mechanisms.15 These protocols allow highly esoteric, non-audio environments—such as the two-dimensional text-based sequencer Orca—to trigger hardware synthesizers, or enable a browser running Strudel to pipe audio-reactive Fast Fourier Transform (FFT) data directly into a Hydra visualizer.17 Furthermore, the emergence of advanced collaborative platforms like Estuary demonstrates a decisive shift toward networked, projectional editing environments. By transmitting semantic AST states rather than raw audio or video, these platforms facilitate global, synchronous, peer-to-peer performances without the latency inherent to traditional network clock synchronization.19  
This comprehensive technical report maps these intricate relationships, defining explicit syntax rules, parsing algorithms, data structure definitions, and interoperability standards to optimize automated system analysis and real-time algorithmic code generation.

## **Key Entities and Relationships**

The algorithmic audiovisual ecosystem is not a monolithic application but rather a distributed matrix of discrete applications, programming languages, and network protocols. These entities function autonomously as standalone pattern generators, or collaboratively as interconnected nodes within a larger, unified synthesis network. Establishing a taxonomy of these entities is critical for defining integration pathways and programmatic capabilities.  
The foundational node within the modern web-based paradigm is **Strudel**. Engineered as a JavaScript port of the TidalCycles live coding language, Strudel operates entirely within a browser-based Read-Eval-Print Loop (REPL).4 To bridge the gap between continuous JavaScript execution and discrete musical timing, Strudel translates raw user input into a dynamic JavaScript Abstract Syntax Tree (AST), which is subsequently evaluated to generate temporal patterns.4 While it possesses a native Web Audio synthesizer known as SuperDough, its architecture is highly modular, supporting external data routing via MIDI, Open Sound Control (OSC), and even MQTT for Internet of Things (IoT) integrations.4  
Serving as the historical and conceptual predecessor to Strudel is **TidalCycles** (frequently abbreviated to Tidal). Embedded deeply within the Haskell functional programming language, Tidal is a highly composable text-based environment.21 Crucially, TidalCycles does not possess the capacity to generate digital signal processing (DSP) audio natively. Instead, its sole function is to calculate mathematical patterns of time and generate formatted OSC messages. These messages are transmitted over a local network loopback to SuperDirt, a feature-rich audio synthesis framework operating within the SuperCollider environment.9 Tidal has inspired an entire open-source family of cyclical time environments, collectively referred to as Uzulangs, of which Strudel is the most prominent web-based derivative.9  
Operating in parallel within the visual domain is **Hydra**, a live code-able video synthesizer conceptualized and written in JavaScript by Olivia Jack.12 Hydra distinguishes itself by bypassing the standard Document Object Model (DOM) rendering pipeline, compiling its chained functional syntax directly into WebGL fragment shaders.12 Its architectural philosophy is heavily inspired by 1970s analog modular synthesis, where complex visual patterns are generated by patching, routing, transforming, and modulating multiple source streams.13 Hydra natively integrates WebRTC, enabling browsers to share video streams peer-to-peer, and utilizes the Meyda library for real-time audio reactivity via Fast Fourier Transform (FFT) analysis.13  
Distinct from the cyclical paradigms of Strudel and Tidal is **Orca**, an esoteric, two-dimensional programming language and procedural sequencer developed by the research studio Hundred Rabbits.23 Orca operates on a spatial grid where the algorithmic logic is dictated by the geographic placement of alphanumeric characters. Each letter of the alphabet represents a discrete mathematical or routing operation.23 Similar to TidalCycles, Orca produces no native sound. It is purely a command sequencer designed to generate MIDI, OSC, and UDP control data to drive external digital audio workstations (DAWs) like Ableton Live, trackers like Renoise, or synthesis engines like VCV Rack.18  
Another hybrid system of note is **Gibber**, a browser-based live coding environment that unifies the semantic structures required for both audio sequencing and visual rendering.25 Gibber is notable for incorporating ray-marching 3D graphics, a technique computationally distinct from Hydra's 2D fragment shaders, and for providing native visual annotations directly within its code editing environment.25 It also offers robust support for networked ensemble performances and the integration of external JavaScript libraries like p5.js.27  
Finally, the ecosystem is supported by specialized collaborative platforms, the most sophisticated being **Estuary**. Estuary is a server-based network collaboration system built upon the Reflex-DOM Haskell framework.19 It acts as a hosting environment for various live coding languages, including a native TidalCycles parser known as "minitidal".28 Estuary introduces the paradigm of projectional, or structure, editing. By treating the code document not as a flat text file but as a live Abstract Syntax Tree, Estuary makes syntactical errors computationally impossible, ensuring that collaborative network performances remain stable regardless of user input errors.19  
The interconnected nature of these environments is managed through specific input and output protocols. Understanding these routing mechanisms is essential for deploying multi-agent code generation. The table below maps the primary relationships, transport protocols, and data payloads that allow these entities to communicate.

| Origin Entity | Destination Entity | Transport Protocol | Data Payload and Output Mechanism |
| :---- | :---- | :---- | :---- |
| **Strudel** | SuperCollider / SuperDirt | OSC via Node.js | UDP network packets transmitting formatted control messages (e.g., /ctrl).15 |
| **Strudel** | Hydra | Web API (Canvas) | Visual pattern feed routed via the initHydra({feedStrudel: 1}) configuration object.17 |
| **Strudel** | Hardware Synthesizers | Web MIDI API | MIDI Note On/Off, Velocity, Channel assignments, and Control Change (CC) parameters.15 |
| **Strudel** | TouchDesigner | Internal Application API | The TLSTRUDEL asset mapping real-time Hap objects (scaled 0.0 to 1.0) directly to user interface parameters.29 |
| **TidalCycles** | SuperDirt | Open Sound Control (OSC) | UDP network packets over localhost port 6010.31 |
| **Orca** | DAWs (e.g., Ableton Live) | MIDI / UDP / OSC | Control Change (!), Pitch Bend (?), Note Data (:), and Monophonic Note Data (%).18 |
| **Hydra** | Distributed Web Browsers | WebRTC | Low-latency, peer-to-peer video signal transmission and screen sharing.13 |
| **Estuary** | Client Browsers | HTTP / WebSockets | State synchronization for collaborative Abstract Syntax Tree rendering.19 |

These systemic relationships imply a strong architectural decoupling of the sequence generator from the synthesis node. Environments such as TidalCycles and Orca act exclusively as mathematical pattern engines, entirely reliant on downstream applications for manifestation. Conversely, environments like Strudel and Gibber operate as comprehensive hybrid systems, housing both the pattern sequencer and the audio/visual synthesis engine within a single JavaScript execution thread, yet retaining the critical ability to decouple and communicate with external hardware or software over standardized network protocols.16

## **Technical Specifications**

The following sections provide granular, protocol-level documentation of the underlying mechanics, syntactic structures, and data processing models required to programmatically interact with and generate code for these respective platforms.

### **Strudel Live Coding Architecture**

Strudel is fundamentally a sophisticated transpilation and scheduling engine designed to circumvent the temporal limitations of the JavaScript runtime environment. While Strudel can theoretically function as a headless library within any JavaScript codebase, its reference implementation and primary user interface is the Strudel REPL.4 The REPL relies on a heavily customized CodeMirror editor dedicated to manipulating patterns in real-time. The core control flow of the Strudel architecture operates in three distinct phases: code transpilation, event scheduling, and output execution.4

#### **Transpilation and AST Generation**

Because standard ECMAScript (JavaScript) lacks the native syntactical brevity required for live musical sequencing, Strudel introduces a transpilation phase to simplify code for the user.4 To generate a Pattern instance from the user's raw JavaScript input, the system performs an automated evaluation process. When the user authors code, the system parses the input using the acorn parsing library to construct a raw Abstract Syntax Tree (AST).4 During this AST traversal, Strudel identifies all double-quoted strings and classifies them as "mini-notation" (e.g., the string "c3 \[e3 g3\]\*2" within the function note("c3 \[e3 g3\]\*2")).4 The transpiler programmatically wraps these identified strings in an internal translation helper function denoted as m(). This helper function accepts the original string as its first parameter and the source code location offset as its second parameter.4 Following this modification, the escodegen library regenerates the AST back into executable JavaScript, resulting in a transpiled statement such as note(m('c3 \[e3 g3\]\*2', 5)).4 Single-quoted strings remain untouched, functioning as standard JavaScript strings.4

#### **The PEG Parser and Location Highlighting**

The string passed into the m() function is written in Strudel's custom mini-notation syntax, which is modeled directly after TidalCycles.4 The processing of this string requires a highly performant parser capable of generating domain-specific language (DSL) outputs. This parser is implemented as a Parsing Expression Grammar (PEG). The grammar definitions reside in the internal mini package, which is built upon the krill project architecture, and compiled using the peggyjs framework.4  
When the generated PEG parser evaluates the mini-notation string, it translates the dense textual representation into a highly detailed AST.4 For instance, parsing "c3 \[e3 g3\]\*2" produces nested AST node types (such as pattern and element) that map directly to native functional JavaScript API calls.4 The string is computationally expanded into an underlying structure resembling: seq(reify('c3').withLoc(6, 9), seq(reify('e3').withLoc(10, 12), reify('g3').withLoc(13, 15)).fast(2)).4  
A critical architectural feature of the Strudel REPL is its visual feedback mechanism, which highlights the specific text currently generating audio.4 This is made computationally possible by the .withLoc() method injected during the PEG parsing phase. The location is calculated dynamically as the sum of the beginning location of the mini-notation string (provided by the acorn parser) and the relative offset of the substring within the mini-notation (provided by peggyjs).4 This absolute character offset allows the REPL's CodeMirror interface to apply visual CSS highlighting in precise synchronization with the audio thread.4

#### **The Scheduling Engine and Haps Execution**

Once the user code is evaluated and a Pattern object is instantiated, the system's Scheduler assumes control. The Strudel Scheduler bridges the abstract pattern definitions with the physical output layer.4 The Scheduler operates by running fixed-interval callbacks on the main JavaScript thread.4 During each tick, it queries the active pattern using the Pattern.queryArc(timespan) method. This method acts as a mathematically pure function that accepts a defined timespan and returns an array of events slated to occur within that window.4 Because queryArc is pure, adjusting the query interval does not alter the resulting audio output, guaranteeing deterministic performance.4  
In the specific nomenclature of the Strudel architecture, an evaluated event is referred to as a Hap.4 A Hap is a discrete, machine-readable JSON object encapsulating the properties of the sound to be generated, alongside its absolute temporal boundaries (beginning time and duration). An example of a generated Hap payload is: { note: 'a4', s: 'sawtooth', gain: 0.5, cutoff: 267 }.32  
A fundamental challenge of browser-based audio scheduling is the inherent instability and imprecision of JavaScript's setInterval function.4 To prevent audio dropouts or timing jitter, Strudel implements a minimum latency buffer variable (minLatency).4 While the scheduler queries the pattern every 50 milliseconds (the default query interval), it schedules the resulting Haps 100 milliseconds into the future (the default minLatency). The execution timestamp is calculated against the highly precise hardware clock provided by the Web Audio API (currentTime + deadline).4 This architecture provides a highly responsive live coding experience—with a real-time latency window between 50ms and 150ms—while guaranteeing sample-accurate audio playback.4

#### **Audio Engine Synthesis and Control Parameters**

The standard output mechanism for Strudel is the native Web Audio API.4 When a Hap reaches its execution deadline, its onTrigger handler is invoked. In the underlying synthesizer mechanism (SuperDough), Strudel dynamically instantiates Web Audio OscillatorNode instances for synthesized sounds, or AudioBufferSourceNode instances for samples.4 The properties of these nodes are mapped directly from the Hap's JSON payload, modifying frequency based on the note parameter, adjusting filter variables based on the cutoff parameter, and connecting the result to the destination hardware.4  
The properties of a Hap are shaped by the user through the application of control parameter functions. These functions utilize method chaining syntax.34 The base pattern establishes the temporal rhythm, and appended methods decorate the events with data. Core control parameters include:

* sound() or s(): Designates the synthesis engine or the target sample bank to be utilized.32  
* note() or n(): Determines the musical pitch for synthesizers, or serves as the integer index selector within a designated sample bank.32  
* gain(): Modulates the amplitude or volume of the resulting event.32  
* cutoff(): Sets the frequency threshold for the Low-Pass Filter, often modulated by internal LFOs.32  
* pan(): Dictates the spatial positioning within the stereo field, mapped as a float where 0 is absolute left, 0.5 is center, and 1 is absolute right.6

When processing sample-based audio, Strudel employs a lazy-loading architecture to conserve browser memory and bandwidth.35 By default, Strudel initializes a comprehensive sample map referencing external databases such as the tidal-drum-machines repository and the Versilian Community Sample Library (VCSL).35 The system maps short string aliases to specific external URLs. However, the heavy .wav or .ogg audio files are not fetched via HTTP requests until the exact moment the user code triggers them.35 Consequently, the initial execution of a new sample may result in momentary silence while the browser resolves the network request.35 Custom local or remote sample banks can be dynamically injected into the runtime using the soundAlias('targetBank', 'alias') function.35  
To facilitate AI integrations and external IDE control, the Strudel ecosystem includes support for the Model Context Protocol (MCP).1 By initializing the Strudel Live Coding MCP server, external agents and LLMs can interact directly with the Strudel runtime.1 The development environment runs a local watcher service that monitors .strudel files. When an external LLM edits the text file, the watcher detects the delta, transmits the updated pattern to the MCP server, and leverages the Playwright browser automation library to instantly evaluate the code within a hidden headless browser instance of the Strudel REPL.1 This provides a seamless pipeline for autonomous, AI-driven algorithmic music generation.

### **TidalCycles and the Haskell Paradigm**

Understanding Strudel requires an analysis of its progenitor, TidalCycles. Authored by Alex McLean, TidalCycles established the fundamental concepts of cyclic time sequencing that dominate the ecosystem.8 Unlike Strudel, which utilizes JavaScript object method chaining (e.g., s("bd").gain(0.5)), Tidal is embedded within Haskell and strictly adheres to pure functional application syntax.6  
In Tidal, the primary operators for constructing patterns are the standard Haskell application operator (\$) and the pattern composition operator (\#).6 The \$ operator applies the function on its left to the argument on its right, bypassing the need for extensive parentheses.6 The \# operator is utilized to merge two distinct patterns. Crucially, \# unifies the structural timing established by the pattern on its left side with the specific parameter values dictated by the pattern on its right side.6  
A standard execution string in TidalCycles takes the following architectural form:

Haskell  
d1 \$ n "0 \[1 2 3\]/3" \# sound "cpu2" \# pan "0 0.5 1"

In this syntax, d1 specifies the designated network connection stream routing OSC packets to the SuperDirt audio engine.6 The function n generates a complex rhythmic pattern of integer notes. The \# sound "cpu2" statement applies the specific cpu2 synthesizer or sample bank to those notes, while \# pan "0 0.5 1" modulates the spatial location across the duration of the cycle.6

### **The Mini-Notation DSL Syntax**

The foundational text-based language for rhythmic sequence generation, universally referred to as the "mini-notation," is syntactically identical across both TidalCycles and Strudel.4 Inspired historically by the polymetric expressions utilized in the Bol Processor software, the mini-notation acts as an incredibly dense, highly compressed domain-specific language for expressing polyrhythms, polymeters, and generative sequences using standard ASCII strings.5  
The mini-notation parser reads specific character tokens as mathematical instructions to subdivide and manipulate a standard temporal cycle.5 The following table defines the core operators, their structural behaviors, and their technical functional equivalents.

| Notation Token | Conceptual Name | Functional Description and Execution Logic | Internal API Equivalent |
| :---- | :---- | :---- | :---- |
| ~ | Rest | Inserts a discrete mathematical rest, maintaining silence for the duration of the current subdivision step.5 | N/A |
| \[ \] | Pattern Grouping | Divides the current temporal step into an isolated sub-cycle, distributing internal elements evenly within that fraction of time.5 | fastcat or cat |
| . | Shorthand Grouping | Performs identical subdivision to \[ \] but utilizes dot syntax to minimize bracket nesting complexity.5 | Sub-pattern division |
| , | Superposition | Instructs the parser to evaluate and play multiple distinct sequences simultaneously within the exact same cycle space, creating polyphony.5 | stack |
| \* | Step Repetition | A multiplier indicating speed; repeats the preceding event  times strictly within its allotted fractional step duration.5 | fast n |
| / | Step Division | A divisor indicating deceleration; stretches the preceding event across  full cycles, fundamentally slowing its execution.5 | slow n |
| \< > | Alternate (Cycle) | Creates a sequence that advances its internal index by exactly one step only upon the commencement of subsequent global cycles.5 | Cycle-based alternating |
| ? | Randomization | Introduces probability, randomly dropping the associated event from the execution stack on any given cycle.5 | degrade |
| : | Sample Selection | Acts as an index selector for loaded sample arrays, pointing to a specific integer element within a defined bank (e.g., hh:3).35 | n("3").s("hh") |
| (n, k) | Euclidean Rhythm | Distributes exactly  active pulses as evenly and symmetrically as mathematically possible across a total of  available structural steps.5 | euclid n k |

The inclusion of the Euclidean Algorithm—formally identified by researcher Godfried Toussaint as generating the structural foundation of traditional global musical rhythms—is a critical feature.5 By inputting the syntax clap(3,5), the system calculates the optimal equidistant placement of three clap events within a five-step grid. This specific mathematical distribution generates complex, historically significant asymmetrical patterns, such as the 13th-century Persian rhythm known as Khafif-e-ramal.6  
While the cyclical paradigm strictly bounds events between a value of 0 and 1 representing a single loop, Strudel has introduced experimental syntax to permit stepwise patterning.42 Under standard cyclical logic, concatenating two patterns (e.g., fastcat("bd hh", "cp hh")) compresses both entirely into a single cycle constraint.42 Utilizing the explicit stepcat() function forces the engine to evaluate elements based on discrete, uncompressed steps, allowing patterns to overflow naturally across cycle boundaries.42 To exert precise control over this overflow, the ^ symbol can be inserted into the mini-notation string. This symbol acts as a marker instructing the parser to utilize a specific, arbitrary hierarchical sub-pattern depth as the foundational metrical level for step counting.42

### **Hydra Video Synthesizer Architecture**

Operating in a distinctly different computational space is Hydra. Hydra entirely abandons traditional HTML5 2D Canvas or SVG rendering paradigms.12 Instead, it functions by passing input code through a parser that dynamically compiles JavaScript directly into raw WebGL fragment shaders.12 This enables highly performant, parallelized pixel-level manipulation natively within the browser hardware GPU.12  
The operational logic of Hydra requires passing visual buffer arrays through a continuous chain of mathematical transformation functions.14 The functional chain is constructed using standard JavaScript dot notation, executing only when the user invokes the specific Ctrl + Shift + Enter key command within the Hydra IDE.44 To facilitate understanding and generative modeling, the Hydra API categorizes its functions into five principal topological domains.43  
The following table provides the required signature mapping for these domains.

| Function Category | Description of Visual Transformation | Key API Functions and Signatures |
| :---- | :---- | :---- |
| **Source** | Generates the foundational pixel matrix or retrieves input from external hardware (e.g., webcams).43 | osc(freq, sync, offset), noise(scale, offset), voronoi(), shape(sides, radius, smoothing), solid(r,g,b,a), src(output) 14 |
| **Geometry** | Alters the spatial coordinate mapping of the visual array, adjusting size, rotation, or repetition.43 | rotate(angle, speed), scale(amount, xMult, yMult), pixelate(x, y), repeat(repeatX, repeatY, offsetX, offsetY), kaleid(nSides) 14 |
| **Color** | Directly manipulates the primary RGB vector values of the underlying fragment shader.43 | color(r,g,b), contrast(amount), luma(threshold, tolerance), posterize(bins, gamma), colorama(amount), saturate(amount) 14 |
| **Blend** | Composites multiple discrete source streams together using varied mathematical blending modes.43 | add(texture, amount), mult(texture, amount), diff(texture), blend(texture, amount), mask(texture) 43 |
| **Modulate** | Utilizes the RGB color values of one visual source to organically distort the physical coordinate mapping of another.43 | modulate(texture, amount), modulateScale(texture, multiple, offset), modulatePixelate(texture, multiple, offset), modulateHue(texture, amount) 14 |

Audio reactivity within the Hydra environment is handled natively without requiring external OSC triggers. The system utilizes the meyda JavaScript library to perform Fast Fourier Transform (FFT) analysis on local microphone inputs or routed system audio.22 The audio context is exposed to the runtime environment via the global a object.22 Developers can configure the precision of the analysis by setting the number of specific frequency bins via a.setBins(n).22  
To prevent visual erraticism caused by raw audio signal noise, the FFT data stream must be managed. Smoothing algorithms are applied dynamically via the a.setSmooth(float) function, where a value of 0.0 translates to instantaneous reaction, and 1.0 permanently freezes the output value.22 Cutoff gates to ignore background noise are managed via a.setCutoff(float).22 To route this data into visual modulation, developers must pass anonymous arrow functions directly into the arguments of Hydra functions.22 For instance, executing the code osc(10, 0, () => a.fft \* 4).out() instructs the WebGL renderer to continuously poll the lowest frequency audio bin (a.fft) and apply its amplified value in real-time to the color offset parameter of the generated oscillator.22

### **Orca: Esoteric Spatial Sequencing**

Contrasting with the text-flow paradigms of Strudel, Tidal, and Hydra, Orca operates as a two-dimensional programming language and cellular automata sequencer.23 Orca utilizes an unconstrained spatial grid where execution relies exclusively on the geographic relationship between characters.18  
The primary operational logic of Orca differentiates between continuous execution and triggered execution based strictly on alphabetical casing.23 An uppercase alphabetical character serves as a continuous function, evaluating its logic on every single internal system frame.23 Conversely, a lowercase alphabetical character acts as a dormant node; it evaluates and executes its payload solely when it receives an adjacent physical "bang" (an activation pulse emitted by a neighboring active cell).23  
As Orca functions entirely as a mathematical routing engine, it contains no internal DSP synthesis capabilities.23 It generates data payloads that are transmitted to the host operating system's network loopback or MIDI driver using specialized syntax tokens.18  
The following table dictates the routing syntax for Orca's output data structures.

| Syntax Token | Output Protocol | Required Data Signature and Execution Result |
| :---- | :---- | :---- |
| : | MIDI | midi(channel octave note velocity length). Transmits a standard polyphonic MIDI Note On message.18 |
| % | MIDI | mono(channel octave note velocity length). Transmits a strict monophonic MIDI Note message.18 |
| ! | MIDI CC | cc(channel knob value). Transmits a continuous MIDI Control Change message for parameter modulation.18 |
| ? | MIDI Pitch Bend | pb(channel value). Transmits MIDI Pitch Bend manipulation data.18 |
| = | OSC | osc(path). Transmits a formatted Open Sound Control message over the network to environments like SuperCollider.18 |
| ; | UDP | udp. Transmits raw User Datagram Protocol networking packets.18 |
| \$ | Internal | self. Triggers an internal Orca macro or command parameter.18 |

### **Network Topologies and Cross-System Integration**

The inherent power of the modern algorithmic live coding ecosystem stems from the ability to bridge these discrete execution engines in real-time. Establishing valid data routes requires strict adherence to networking protocols.

#### **Open Sound Control (OSC) Specifications**

When bridging environments like TidalCycles, Strudel, or Orca into the SuperCollider/SuperDirt engine, data is serialized into OSC packets.31 To initiate a broadcast from Tidal, the system initializes an active OSC stream using the command tidal \<- startTidal superdirtTarget.31  
The standard required message path defined by the SuperDirt listener is /ctrl.31 SuperDirt’s OSC parsers rely heavily on specific type tags to decode incoming packet payloads accurately. For example, applying the type tag sf instructs the receiving listener that the payload contains exactly one String immediately followed by one Float.31 Sending the data packet /ctrl sf hello 0.4 instructs SuperDirt to isolate the synthesizer parameter named hello and update its internal state to the float value 0.4. Back within the Tidal interface, this specific targeted variable can be patterned algorithmically via the continuous float function cF 1 "hello".31

#### **MIDI Data Streams**

Both Strudel and Tidal generate highly accurate MIDI data. To redirect Strudel's native audio outputs to external hardware synthesizers or DAWs, the developer appends the .midi() function to the end of the method chain.15 The Web MIDI API demands explicit user authorization via browser security protocols before execution.15 Once authorized, the midi(outputName?) function automatically polls the operating system for available MIDI Out ports. If no string argument is provided, the function binds to the very first available hardware port (e.g., the IAC Driver on macOS or MIDI Through Port on Linux).15 Deep hardware parameters can be manipulated by sending System Exclusive (SysEx) messages. The sysexid and sysexdata parameters require data arrays consisting of integers strictly bound between 0 and 255, representing the raw hexadecimal byte codes.16  
In contrast, Tidal routes its MIDI payloads directly through SuperCollider.46 The SuperCollider MIDI channel parameters operate on a strictly 0-indexed integer scale. Therefore, instructing external hardware to execute notes on MIDI Channel 1 necessitates utilizing the specific syntax \# midichan 0, while MIDI Channel 5 requires \# midichan 4.46 The SuperDirt framework natively scales standard 0-127 MIDI Control Change (CC) incoming values into a normalized float range of 0.0 to 1.0.47

#### **Strudel to Hydra Integration**

Strudel and Hydra possess native interoperability, allowing them to be fused into a singular browser execution environment where Strudel's mathematical audio arrays dictate Hydra's visual matrices. This bridge is initialized asynchronously at the top of the Strudel REPL using the command await initHydra().17  
To explicitly pipe data streams between the two engines, the initialization command must accept a defined configuration object: await initHydra({feedStrudel: 1, detectAudio: true}).17

> 1. **Visual Routing (feedStrudel):** Enabling this parameter captures the HTML5 Canvas visual output of Strudel's internal UI elements—such as its piano roll rendering or the generative .spiral() visualizer component—and maps them directly to Hydra's internal visual source designated as s0.17 The programmer can immediately manipulate the Strudel UI visually by executing Hydra operators against it: src(s0).kaleid(4).out().17  
> 2. **Pattern Data Routing (H()):** Strudel exposes a dedicated bridge function, H(), which actively converts a Strudel mini-notation string into a dynamic array digestible by Hydra's mathematical algorithms. For instance, writing kaleid(H("\<4 5 6>")) passes the rhythmically stepping integer pattern directly into the Hydra kaleidoscope node's face count parameter, perfectly synchronizing the geometrical mirroring with the underlying audio clock cycle.17

#### **External Environment Integration and TouchDesigner**

The abstract scheduling data generated by Strudel can be exported to enterprise visual environments such as TouchDesigner. Community-developed architectures like the TLSTRUDEL asset embed the entire Strudel REPL directly into a localized TouchDesigner node tree, completely bypassing the requirement for standalone OSC network bridges.29 This specialized asset captures the raw, continuous 0.0 to 1.0 float arrays representing the precise trigger states of all active scheduled Haps. This allows developers to map granular Strudel sonic events directly to TouchDesigner Channel Operators (CHOPs), enabling generative visual geometries and 3D physics engines to react instantaneously to live coded algorithms.29

#### **Collaborative Network Topologies**

Network-based live coding environments resolve the prohibitive latency issues inherent to synchronous remote musical collaboration by transmitting lightweight semantic state instructions rather than streaming raw, heavy audio data.19  
The platform **Estuary** implements an architecture predicated on "projectional editing" and "structure editing".19 Instead of treating a live-coding document as an unstructured array of vulnerable ASCII characters, Estuary’s Reflex-DOM framework evaluates the document continuously as a live Abstract Syntax Tree.19 The strict structure editing protocol physically prevents syntactical invalidity; programmers can only inject mathematically valid tokens into contextually meaningful structural slots within the UI.19 By hosting a parser known as "minitidal," the P2P architecture connects users globally. Each participant's browser independently compiles and executes the code locally, rendering network clock synchronization latency irrelevant, as only the text deltas and timestamp metadata traverse the HTTP/WebRTC pathways.19  
Other notable network collaborative architectures include **Troop**, designed specifically for synchronized group terminal execution, and **Flok**, a P2P web editor built specifically to synchronize local instances of Hydra and SuperCollider.50 **Gibber** inherently supports networked ensemble capabilities, rendering synchronized dual A/V outputs across remote clients while centralizing the underlying JS synthesis framework.25

## **Prompting Context**

To ensure high-fidelity context retrieval and to condition Large Language Models effectively when synthesizing functional, executable code for these specialized environments, the following systemic constraints, semantic maps, and syntactical rulesets must be rigorously observed.

### **Semantic Mapping and Temporal Vocabulary**

* **Time and Tempo Definition:** Temporal architecture within these algorithmic environments must never be referenced using standard DAW terminology such as Beats Per Minute (BPM). The fundamental mathematical constant governing global playback across these frameworks is **CPS (Cycles Per Second)**.3 All calculations adjusting temporal flow must utilize the primary formula setcps(x) or the translation formula setcpm(bpm/bpc) where bpc specifically denotes the number of perceived beats per individual cycle.3  
* **Event Instantiation:** Within the Strudel architecture specifically, an audio or data event is definitively classified as a **Hap**.4 A Hap must be conceptualized not as audio, but as a discrete, machine-readable JSON object encapsulating note values, parameter offsets, source paths, and an absolute timespan.4  
* **String Parsing:** Text wrapped within standard quotation marks inside Strudel or Tidal must be definitively recognized as **mini-notation**.4 The generating agent must comprehend that this notation triggers the internal PEG parser, expanding into an AST utilizing nested functional arrays, and is not evaluated as standard string data.4

### **Architectural Constraints for Automated Code Generation**

When outputting programmatic responses, the language model must adhere to the severe syntactical boundaries delineated by the user's specific target environment. Failure to adhere to these boundaries will result in compilation failure.

* **Strudel Execution Rules:**  
  * Code generation must strictly adhere to JavaScript object method chaining. Successive functional operations must be appended linearly via dot notation (e.g., s("bd").note("c3").gain(0.8)).34  
  * The core output terminator must explicitly dictate the routing protocol using .out(), .midi(), or .osc().15  
  * Variables passed to the core s() or sound() parameter must precisely match the default string aliases established by the internal tidal-drum-machines or VCSL mapping protocols (e.g., bd, sd, hh, cp, rim).35  
* **TidalCycles Execution Rules:**  
  * Code generation must entirely avoid JavaScript chaining and strictly utilize Haskell pure functional application operators, specifically the \$ and \# symbols.6  
  * The terminal output execution must target a discrete, pre-configured channel designation (e.g., d1, d2) mapped to the SuperDirt OSC listener.6  
* **Hydra Execution Rules:**  
  * The functional pipeline must mathematically progress through the Source -> Geometry -> Color -> Blend -> Modulate topological sequence.43  
  * Ensure that any anonymous audio-reactive logic constructed using lambda functions (e.g., () => a.fft\[n\]) points strictly to valid array indices defined by earlier a.setBins() boundary declarations.22  
  * All terminal nodes must explicitly command the renderer to broadcast to an active output channel via the .out(o0), .out(o1), etc., termination functions.14  
* **Orca Execution Rules:**  
  * Output generation must completely avoid standard text-flow syntax. Patterns must be formatted spatially as monospaced ASCII geometric grids.23  
  * Strict adherence to the internal alphabetic command operations must be maintained, observing the casing rules defining Frame vs. Bang execution states, and routing operators (e.g., : for MIDI, = for OSC).18

### **Structural Evaluation and Mini-Notation Optimization**

The generative model must internalize the logic that specific mini-notation symbols directly invoke structural mathematical transformations. Creating a polymetric rhythm or generating a Euclidean progression requires understanding that the string representation acts as an array of algorithmic instructions. Generating the syntax "(n, k)" forces the internal scheduler to execute a highly complex routine that distributes exactly  triggers symmetrically across a time arc comprising  subdivisions.6 LLMs should systematically prioritize the generation of mini-notation over standard programmatic array construction for rhythmic mapping. This approach optimizes token efficiency, conforms to standard live-coding architectural conventions, and ensures maximal utilization of the JIT parser mechanics.5

## **Iterative Refinement**

Following the ingestion and contextualization of this exhaustive architectural documentation, please specify if any specific data modules require structural adjustments. Indicate whether further technical depth is required regarding the Abstract Syntax Tree generation phases, the specific UDP network packet schemas utilized during OSC loopback, or the mathematical smoothing algorithms applied within the Meyda audio analysis pipeline to optimize continuous autonomous prompt generation.

#### **Works cited**

> 1. From Prompt to Beat: An AI Engineer's Guide to the Strudel Live Coding MCP Server, accessed on May 29, 2026, [https://skywork.ai/skypage/en/ai-engineer-guide-strudel-live-coding/1981613226920611840](https://skywork.ai/skypage/en/ai-engineer-guide-strudel-live-coding/1981613226920611840)  
> 2. Tools for Live Coding - Livecode.NYC, accessed on May 29, 2026, [https://livecode.nyc/tools](https://livecode.nyc/tools)  
> 3. Understanding Cycles Strudel, accessed on May 29, 2026, [https://strudel.cc/understand/cycles/](https://strudel.cc/understand/cycles/)  
> 4. REPL Strudel, accessed on May 29, 2026, [https://strudel.cc/technical-manual/repl/](https://strudel.cc/technical-manual/repl/)  
> 5. Mini Notation | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/reference/mini\_notation/](https://tidalcycles.org/docs/reference/mini_notation/)  
> 6. Course I (> 1.6) - Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/patternlib/tutorials/course1/](https://tidalcycles.org/docs/patternlib/tutorials/course1/)  
> 7. Learning Tidal Fundamentals, accessed on May 29, 2026, [https://mzadel.github.io/tidalfundamentals/](https://mzadel.github.io/tidalfundamentals/)  
> 8. Strudel: JavaScript Live Coding Music | PDF | Parsing | Computer Programming - Scribd, accessed on May 29, 2026, [https://www.scribd.com/document/905790077/Strudel](https://www.scribd.com/document/905790077/Strudel)  
> 9. Live code with Tidal Cycles | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/](https://tidalcycles.org/)  
> 10. Strudel REPL, accessed on May 29, 2026, [https://strudel.cc/](https://strudel.cc/)  
> 11. Getting Started Strudel Docs, accessed on May 29, 2026, [https://patterns.slab.org/learn/getting-started/](https://patterns.slab.org/learn/getting-started/)  
> 12. hydra video synth: documentation portal, accessed on May 29, 2026, [https://hydra.ojack.xyz/docs/](https://hydra.ojack.xyz/docs/)  
> 13. Hydra: live coding networked visuals - Zenodo, accessed on May 29, 2026, [https://zenodo.org/records/3946269](https://zenodo.org/records/3946269)  
> 14. hydra video synth | olivia jack, accessed on May 29, 2026, [https://ojack.xyz/work/hydra/](https://ojack.xyz/work/hydra/)  
> 15. MIDI & OSC Strudel - GitHub Pages, accessed on May 29, 2026, [https://urswilke.github.io/strudel/learn/input-output/](https://urswilke.github.io/strudel/learn/input-output/)  
> 16. MIDI, OSC & MQTT Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/input-output/](https://strudel.cc/learn/input-output/)  
> 17. Hydra Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/hydra/](https://strudel.cc/learn/hydra/)  
> 18. hundredrabbits/Orca: Esoteric Programming Language - GitHub, accessed on May 29, 2026, [https://github.com/hundredrabbits/Orca](https://github.com/hundredrabbits/Orca)  
> 19. Estuary: Browser-based Collaborative Projectional Live Coding of Musical Patterns, accessed on May 29, 2026, [https://iclc.toplap.org/2017/cameraReady/ICLC\_2017\_paper\_78.pdf](https://iclc.toplap.org/2017/cameraReady/ICLC_2017_paper_78.pdf)  
> 20. Intro to Estuary Collaborative Live Coding Platform - YouTube, accessed on May 29, 2026, [https://www.youtube.com/watch?v=vdKNDZE--g0](https://www.youtube.com/watch?v=vdKNDZE--g0)  
> 21. Documentation - TidalCycles userbase, accessed on May 29, 2026, [https://userbase.tidalcycles.org/Welcome/en.html](https://userbase.tidalcycles.org/Welcome/en.html)  
> 22. hydra-synth/hydra: Livecoding networked visuals in the browser - GitHub, accessed on May 29, 2026, [https://github.com/hydra-synth/hydra](https://github.com/hydra-synth/hydra)  
> 23. Orca by Rek & Devine - Itch.io, accessed on May 29, 2026, [https://hundredrabbits.itch.io/orca](https://hundredrabbits.itch.io/orca)  
> 24. LEARN ORCΛ~, accessed on May 29, 2026, [https://metasyn.srht.site/learn-orca/](https://metasyn.srht.site/learn-orca/)  
> 25. gibber, accessed on May 29, 2026, [https://gibber.cc/](https://gibber.cc/)  
> 26. gibber-cc/gibber: An audiovisual live coding environment for the browser - GitHub, accessed on May 29, 2026, [https://github.com/gibber-cc/gibber](https://github.com/gibber-cc/gibber)  
> 27. Gibber | Live Coding Blog, accessed on May 29, 2026, [https://blog.livecoding.nyuadim.com/2024/02/16/gibber/](https://blog.livecoding.nyuadim.com/2024/02/16/gibber/)  
> 28. Friends and relations | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/resource/Friends\_and\_relations/](https://tidalcycles.org/docs/resource/Friends_and_relations/)  
> 29. TLSTRUDEL - STRUDEL TO TD | Derivative - TouchDesigner, accessed on May 29, 2026, [https://derivative.ca/community-post/asset/tlstrudel-strudel-td/74064](https://derivative.ca/community-post/asset/tlstrudel-strudel-td/74064)  
> 30. TLSTRUDEL - STRUDEL TO TD - YouTube, accessed on May 29, 2026, [https://www.youtube.com/watch?v=XJYcRn8lwec](https://www.youtube.com/watch?v=XJYcRn8lwec)  
> 31. OSC - Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/configuration/MIDIOSC/osc/](https://tidalcycles.org/docs/configuration/MIDIOSC/osc/)  
> 32. Technical Manual · tidalcycles/strudel Wiki - GitHub, accessed on May 29, 2026, [https://github.com/tidalcycles/strudel/wiki/Technical-Manual](https://github.com/tidalcycles/strudel/wiki/Technical-Manual)  
> 33. REPL Strudel Docs, accessed on May 29, 2026, [https://patterns.slab.org/technical-manual/repl/](https://patterns.slab.org/technical-manual/repl/)  
> 34. Coding syntax Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/code/](https://strudel.cc/learn/code/)  
> 35. Samples Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/samples/](https://strudel.cc/learn/samples/)  
> 36. Tutorial | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/getting-started/tutorial/](https://tidalcycles.org/docs/getting-started/tutorial/)  
> 37. Strudel Live Coding — MCP Server — MCP.Directory, accessed on May 29, 2026, [https://mcp.directory/servers/strudel-live-coding](https://mcp.directory/servers/strudel-live-coding)  
> 38. GitHub - therebelrobot/strudelplay: ‍ Use your own code editor to live-code in Strudel! Live-reloading, file separation, auto-server for local samples, and more!, accessed on May 29, 2026, [https://github.com/therebelrobot/strudelplay](https://github.com/therebelrobot/strudelplay)  
> 39. Alex McLean - Haskell Foundation, accessed on May 29, 2026, [https://haskell.foundation/podcast/67/](https://haskell.foundation/podcast/67/)  
> 40. Mini-notation references - TOPLAP forum, accessed on May 29, 2026, [https://forum.toplap.org/t/mini-notation-references/2384](https://forum.toplap.org/t/mini-notation-references/2384)  
> 41. Getting Started Strudel, accessed on May 29, 2026, [https://strudel.cc/workshop/getting-started/](https://strudel.cc/workshop/getting-started/)  
> 42. Stepwise patterning Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/stepwise/](https://strudel.cc/learn/stepwise/)  
> 43. Hydra Functions, accessed on May 29, 2026, [https://hydra.ojack.xyz/functions/](https://hydra.ojack.xyz/functions/)  
> 44. LPb74TsAP, accessed on May 29, 2026, [https://hydra.ojack.xyz/](https://hydra.ojack.xyz/)  
> 45. Using ORCA (ORCΛ) livecoding sequencer to control some instruments in my DAW - Reddit, accessed on May 29, 2026, [https://www.reddit.com/r/synthesizers/comments/mu350f/using\_orca\_orc%CE%BB\_livecoding\_sequencer\_to\_control/](https://www.reddit.com/r/synthesizers/comments/mu350f/using_orca_orc%CE%BB_livecoding_sequencer_to_control/)  
> 46. MIDI | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/configuration/MIDIOSC/midi/](https://tidalcycles.org/docs/configuration/MIDIOSC/midi/)  
> 47. Week 6, lesson 4 - controlling Tidal with MIDI - Uzu, accessed on May 29, 2026, [https://uzu.lurk.org/t/week-6-lesson-4-controlling-tidal-with-midi/1260](https://uzu.lurk.org/t/week-6-lesson-4-controlling-tidal-with-midi/1260)  
> 48. Visual Feedback Strudel, accessed on May 29, 2026, [https://strudel.cc/learn/visual-feedback/](https://strudel.cc/learn/visual-feedback/)  
> 49. Strudel Blog, accessed on May 29, 2026, [https://strudel.patternclub.org/blog/](https://strudel.patternclub.org/blog/)  
> 50. Multi-User Tidal | Tidal Cycles, accessed on May 29, 2026, [https://tidalcycles.org/docs/configuration/multiuser-tidal/](https://tidalcycles.org/docs/configuration/multiuser-tidal/)  
> 51. Visuals for beginners? - TidalCycles - Uzu, accessed on May 29, 2026, [https://uzu.lurk.org/t/visuals-for-beginners/2401](https://uzu.lurk.org/t/visuals-for-beginners/2401)  
> 52. Introduction Strudel Docs, accessed on May 29, 2026, [https://patterns.slab.org/functions/intro/](https://patterns.slab.org/functions/intro/)
