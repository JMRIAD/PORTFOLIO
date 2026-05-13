# 🖧 Topologie Réseau — Gestion de Parc Informatique

> **Contexte** : Ce dépôt présente la configuration d'une topologie réseau d'entreprise avec segmentation VLAN, routage inter-VLAN (Router-on-a-Stick), et une analyse complète de la gestion du parc informatique via GLPI.

---

## 📋 Table des matières

1. [Présentation de la topologie](#-présentation-de-la-topologie)
2. [Configuration réseau](#-configuration-réseau)
3. [Rôle de GLPI dans ce scénario](#-rôle-de-glpi-dans-ce-scénario)
4. [Incidents potentiels](#-incidents-potentiels)
5. [Vulnérabilités et problèmes sous-jacents](#-vulnérabilités-et-problèmes-sous-jacents)
6. [Corrections et bonnes pratiques](#-corrections-et-bonnes-pratiques)
7. [Changements de configuration recommandés](#-changements-de-configuration-recommandés)
8. [Normes internationales appliquées](#-normes-internationales-appliquées)

---

## 🗺 Présentation de la topologie

### Schéma général

```
                        [Internet / WAN]
                               |
                           [Pare-feu]
                               |
                    [Routeur Principal R1]
                    (Router-on-a-Stick)
                               |
                    [Switch Core SW-CORE]
                    /          |          \
              [SW-ACC1]    [SW-ACC2]    [SW-ACC3]
                 |              |             |
           VLAN 10          VLAN 20        VLAN 30
        (Direction)     (Informatique)  (Utilisateurs)
```

> <svg width="100%" viewBox="0 0 680 540" role="img" xmlns="http://www.w3.org/2000/svg">
<title style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">Capture Packet Tracer — topologie réseau entreprise</title>
<desc style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">Simulation d'une capture d'écran Packet Tracer avec routeur R1, switch core, 3 switches d'accès et postes par VLAN</desc>

<rect x="0" y="0" width="680" height="540" fill="#2B2B2B" style="fill:rgb(43, 43, 43);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<rect x="0" y="0" width="680" height="22" fill="#3C3C3C" style="fill:rgb(60, 60, 60);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<rect x="0" y="22" width="680" height="18" fill="#4A4A4A" style="fill:rgb(74, 74, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<rect x="0" y="490" width="680" height="50" fill="#1E1E1E" style="fill:rgb(30, 30, 30);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<text x="10" y="14" font-family="monospace" font-size="11" fill="#CCCCCC" style="fill:rgb(204, 204, 204);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:11px;font-weight:400;text-anchor:start;dominant-baseline:auto">File  Edit  Options  View  Tools  Extensions  Help</text>
<text x="10" y="35" font-family="monospace" font-size="10" fill="#AAAAAA" style="fill:rgb(170, 170, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:10px;font-weight:400;text-anchor:start;dominant-baseline:auto">Logical  Physical  |  Select  Move  Delete  Inspect  Add Note  |  Zoom: 100%</text>

<rect x="0" y="40" width="680" height="450" fill="#1A1F2E" style="fill:rgb(26, 31, 46);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<rect x="8" y="48" width="92" height="436" fill="#232A3A" stroke="#3A4255" stroke-width="0.5" style="fill:rgb(35, 42, 58);stroke:rgb(58, 66, 85);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="54" y="62" text-anchor="middle" font-family="monospace" font-size="9" fill="#7A8BAA" style="fill:rgb(122, 139, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">DEVICES</text>
<rect x="14" y="70" width="80" height="1" fill="#3A4255" style="fill:rgb(58, 66, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<rect x="16" y="78" width="76" height="40" rx="4" fill="#2A3347" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(42, 51, 71);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="54" y="94" text-anchor="middle" font-family="monospace" font-size="8" fill="#4A90D9" style="fill:rgb(74, 144, 217);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Routers</text>
<text x="54" y="108" text-anchor="middle" font-family="monospace" font-size="7" fill="#7A8BAA" style="fill:rgb(122, 139, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">2911, 1941...</text>

<rect x="16" y="124" width="76" height="40" rx="4" fill="#2A3347" stroke="#3A4255" stroke-width="0.5" style="fill:rgb(42, 51, 71);stroke:rgb(58, 66, 85);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="54" y="140" text-anchor="middle" font-family="monospace" font-size="8" fill="#8899AA" style="fill:rgb(136, 153, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Switches</text>
<text x="54" y="154" text-anchor="middle" font-family="monospace" font-size="7" fill="#7A8BAA" style="fill:rgb(122, 139, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">2960, 3560...</text>

<rect x="16" y="170" width="76" height="40" rx="4" fill="#2A3347" stroke="#3A4255" stroke-width="0.5" style="fill:rgb(42, 51, 71);stroke:rgb(58, 66, 85);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="54" y="186" text-anchor="middle" font-family="monospace" font-size="8" fill="#8899AA" style="fill:rgb(136, 153, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">End Devices</text>
<text x="54" y="200" text-anchor="middle" font-family="monospace" font-size="7" fill="#7A8BAA" style="fill:rgb(122, 139, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC, Server...</text>

<rect x="16" y="216" width="76" height="40" rx="4" fill="#2A3347" stroke="#3A4255" stroke-width="0.5" style="fill:rgb(42, 51, 71);stroke:rgb(58, 66, 85);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="54" y="232" text-anchor="middle" font-family="monospace" font-size="8" fill="#8899AA" style="fill:rgb(136, 153, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Connections</text>
<text x="54" y="246" text-anchor="middle" font-family="monospace" font-size="7" fill="#7A8BAA" style="fill:rgb(122, 139, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Copper, Fiber...</text>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="298" y="58" width="84" height="52" rx="4" fill="#1E2535" stroke="#4A90D9" stroke-width="1.5" style="fill:rgb(30, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="306" y="64" width="68" height="28" rx="2" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="310" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="320" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="330" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="340" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="350" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="360" y="67" width="8" height="6" rx="1" fill="#1A2A4A" style="fill:rgb(26, 42, 74);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="310" y="76" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="318" y="76" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="326" y="76" width="6" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="334" y="76" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="342" y="76" width="6" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="350" y="76" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="340" y="102" text-anchor="middle" font-family="monospace" font-size="9" fill="#EAEAEA" style="fill:rgb(234, 234, 234);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">R1</text>
  <text x="340" y="114" text-anchor="middle" font-family="monospace" font-size="7" fill="#7AABEE" style="fill:rgb(122, 171, 238);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Cisco 2911</text>
</g>

<line x1="340" y1="110" x2="340" y2="148" stroke="#F0C040" stroke-width="2.5" style="fill:rgb(0, 0, 0);stroke:rgb(240, 192, 64);color:rgb(255, 255, 255);stroke-width:2.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="278" y="148" width="124" height="40" rx="3" fill="#1E2535" stroke="#8855EE" stroke-width="1.5" style="fill:rgb(30, 37, 53);stroke:rgb(136, 85, 238);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="284" y="152" width="112" height="20" rx="2" fill="#2A2040" style="fill:rgb(42, 32, 64);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="288" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="296" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="304" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="312" y="154" width="6" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="320" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="328" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="336" y="154" width="6" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="344" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="352" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="360" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="368" y="154" width="6" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="340" y="181" text-anchor="middle" font-family="monospace" font-size="9" fill="#EAEAEA" style="fill:rgb(234, 234, 234);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SW-CORE</text>
</g>

<line x1="230" y1="168" x2="155" y2="220" stroke="#F0C040" stroke-width="2" style="fill:rgb(0, 0, 0);stroke:rgb(240, 192, 64);color:rgb(255, 255, 255);stroke-width:2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="340" y1="188" x2="340" y2="220" stroke="#F0C040" stroke-width="2" style="fill:rgb(0, 0, 0);stroke:rgb(240, 192, 64);color:rgb(255, 255, 255);stroke-width:2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="450" y1="168" x2="525" y2="220" stroke="#F0C040" stroke-width="2" style="fill:rgb(0, 0, 0);stroke:rgb(240, 192, 64);color:rgb(255, 255, 255);stroke-width:2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="88" y="220" width="134" height="36" rx="3" fill="#1E2535" stroke="#22AA66" stroke-width="1.5" style="fill:rgb(30, 37, 53);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="94" y="224" width="116" height="16" rx="2" fill="#1A2D20" style="fill:rgb(26, 45, 32);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="98" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="105" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="112" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="119" y="226" width="5" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="126" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="133" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="140" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="147" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="154" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="161" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="155" y="248" text-anchor="middle" font-family="monospace" font-size="9" fill="#EAEAEA" style="fill:rgb(234, 234, 234);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SW-ACC1</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="278" y="220" width="124" height="36" rx="3" fill="#1E2535" stroke="#22AA66" stroke-width="1.5" style="fill:rgb(30, 37, 53);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="284" y="224" width="112" height="16" rx="2" fill="#1A2D20" style="fill:rgb(26, 45, 32);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="288" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="295" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="302" y="226" width="5" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="309" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="316" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="323" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="330" y="226" width="5" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="337" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="344" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="351" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="340" y="248" text-anchor="middle" font-family="monospace" font-size="9" fill="#EAEAEA" style="fill:rgb(234, 234, 234);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SW-ACC2</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="458" y="220" width="134" height="36" rx="3" fill="#1E2535" stroke="#22AA66" stroke-width="1.5" style="fill:rgb(30, 37, 53);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="464" y="224" width="122" height="16" rx="2" fill="#1A2D20" style="fill:rgb(26, 45, 32);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="468" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="475" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="482" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="489" y="226" width="5" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="496" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="503" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="510" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="517" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="524" y="226" width="5" height="4" rx="1" fill="#FFAA00" style="fill:rgb(255, 170, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="531" y="226" width="5" height="4" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="525" y="248" text-anchor="middle" font-family="monospace" font-size="9" fill="#EAEAEA" style="fill:rgb(234, 234, 234);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SW-ACC3</text>
</g>

<line x1="118" y1="256" x2="100" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="145" y1="256" x2="155" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="172" y1="256" x2="210" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<line x1="306" y1="256" x2="280" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="332" y1="256" x2="332" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="358" y1="256" x2="390" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<line x1="492" y1="256" x2="468" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="518" y1="256" x2="520" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<line x1="544" y1="256" x2="572" y2="306" stroke="#22AAFF" stroke-width="1.5" style="fill:rgb(0, 0, 0);stroke:rgb(34, 170, 255);color:rgb(255, 255, 255);stroke-width:1.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="76" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="82" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="78" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="82" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="98" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-DIR-01</text>
  <text x="98" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.10.11</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="130" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="136" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="132" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="136" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="152" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-DIR-02</text>
  <text x="152" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.10.12</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="186" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="192" y="308" width="26" height="18" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="194" y="310" width="22" height="12" rx="1" fill="#1A2A45" style="fill:rgb(26, 42, 69);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="196" y="326" width="18" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="208" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">LAPTOP</text>
  <text x="208" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.10.13</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="256" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#FFAA22" stroke-width="1" style="fill:rgb(26, 37, 53);stroke:rgb(255, 170, 34);color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="260" y="310" width="8" height="20" rx="1" fill="#2A3A2A" style="fill:rgb(42, 58, 42);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="270" y="312" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="270" y="317" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="270" y="322" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="280" y="310" width="6" height="6" rx="1" fill="#003311" style="fill:rgb(0, 51, 17);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="280" y="318" width="6" height="6" rx="1" fill="#003311" style="fill:rgb(0, 51, 17);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="280" y="326" width="6" height="6" rx="1" fill="#00AA44" style="fill:rgb(0, 170, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="278" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#FFCC66" style="fill:rgb(255, 204, 102);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SRV-GLPI</text>
  <text x="278" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#AA8833" style="fill:rgb(170, 136, 51);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.20.10</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="310" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#FFAA22" stroke-width="1" style="fill:rgb(26, 37, 53);stroke:rgb(255, 170, 34);color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="314" y="310" width="8" height="20" rx="1" fill="#2A3A2A" style="fill:rgb(42, 58, 42);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="324" y="312" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="324" y="317" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="324" y="322" width="8" height="3" rx="1" fill="#006622" style="fill:rgb(0, 102, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="334" y="310" width="6" height="6" rx="1" fill="#003311" style="fill:rgb(0, 51, 17);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="334" y="318" width="6" height="6" rx="1" fill="#003311" style="fill:rgb(0, 51, 17);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="334" y="326" width="6" height="6" rx="1" fill="#00AA44" style="fill:rgb(0, 170, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="332" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#FFCC66" style="fill:rgb(255, 204, 102);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">SRV-ZBX</text>
  <text x="332" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#AA8833" style="fill:rgb(170, 136, 51);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.20.20</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="366" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="372" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="368" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="372" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="388" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-TECH</text>
  <text x="388" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.20.30</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="444" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="450" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="446" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="450" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="466" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-USR-01</text>
  <text x="466" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.30.11</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="496" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="502" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="498" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="502" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="518" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-USR-02</text>
  <text x="518" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.30.12</text>
</g>

<g style="fill:rgb(0, 0, 0);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto">
  <rect x="548" y="306" width="44" height="36" rx="3" fill="#1A2535" stroke="#4A90D9" stroke-width="0.5" style="fill:rgb(26, 37, 53);stroke:rgb(74, 144, 217);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="554" y="310" width="20" height="14" rx="1" fill="#2A3A5A" style="fill:rgb(42, 58, 90);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="550" y="324" width="28" height="4" rx="1" fill="#3A4A6A" style="fill:rgb(58, 74, 106);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <rect x="554" y="330" width="10" height="4" rx="1" fill="#2A3355" style="fill:rgb(42, 51, 85);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
  <text x="570" y="350" text-anchor="middle" font-family="monospace" font-size="7" fill="#88AADD" style="fill:rgb(136, 170, 221);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">PC-USR-03</text>
  <text x="570" y="360" text-anchor="middle" font-family="monospace" font-size="6" fill="#556688" style="fill:rgb(85, 102, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:6px;font-weight:400;text-anchor:middle;dominant-baseline:auto">.30.13</text>
</g>

<rect x="108" y="374" width="120" height="28" rx="3" fill="#162A1E" stroke="#22AA66" stroke-width="0.5" stroke-dasharray="3 2" style="fill:rgb(22, 42, 30);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-dasharray:3px, 2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="168" y="385" text-anchor="middle" font-family="monospace" font-size="8" fill="#22AA66" style="fill:rgb(34, 170, 102);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">VLAN 10 — Direction</text>
<text x="168" y="396" text-anchor="middle" font-family="monospace" font-size="7" fill="#1D9E75" style="fill:rgb(29, 158, 117);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">192.168.10.0/24</text>

<rect x="252" y="374" width="130" height="28" rx="3" fill="#2A2010" stroke="#FFAA22" stroke-width="0.5" stroke-dasharray="3 2" style="fill:rgb(42, 32, 16);stroke:rgb(255, 170, 34);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-dasharray:3px, 2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="317" y="385" text-anchor="middle" font-family="monospace" font-size="8" fill="#FFAA22" style="fill:rgb(255, 170, 34);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">VLAN 20 — Informatique</text>
<text x="317" y="396" text-anchor="middle" font-family="monospace" font-size="7" fill="#CC8833" style="fill:rgb(204, 136, 51);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">192.168.20.0/24</text>

<rect x="440" y="374" width="120" height="28" rx="3" fill="#162A1E" stroke="#22AA66" stroke-width="0.5" stroke-dasharray="3 2" style="fill:rgb(22, 42, 30);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-dasharray:3px, 2px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="500" y="385" text-anchor="middle" font-family="monospace" font-size="8" fill="#22AA66" style="fill:rgb(34, 170, 102);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">VLAN 30 — Utilisateurs</text>
<text x="500" y="396" text-anchor="middle" font-family="monospace" font-size="7" fill="#1D9E75" style="fill:rgb(29, 158, 117);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:7px;font-weight:400;text-anchor:middle;dominant-baseline:auto">192.168.30.0/24</text>

<rect x="0" y="490" width="680" height="50" fill="#1E1E1E" style="fill:rgb(30, 30, 30);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<rect x="0" y="490" width="680" height="1" fill="#3A3A3A" style="fill:rgb(58, 58, 58);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="10" y="505" font-family="monospace" font-size="9" fill="#AAAAAA" style="fill:rgb(170, 170, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:9px;font-weight:400;text-anchor:start;dominant-baseline:auto">Logical Workspace  |  Real-time Mode</text>
<rect x="540" y="495" width="12" height="8" rx="1" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="555" y="503" font-family="monospace" font-size="8" fill="#00CC44" style="fill:rgb(0, 204, 68);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:start;dominant-baseline:auto">All devices up</text>
<text x="10" y="520" font-family="monospace" font-size="8" fill="#888888" style="fill:rgb(136, 136, 136);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:start;dominant-baseline:auto">R1: IOS 15.7  |  SW-CORE: IOS 12.2  |  Topology saved: reseau_entreprise.pkt</text>
<rect x="574" y="513" width="92" height="16" rx="3" fill="#2A6A2A" stroke="#22AA66" stroke-width="0.5" style="fill:rgb(42, 106, 42);stroke:rgb(34, 170, 102);color:rgb(255, 255, 255);stroke-width:0.5px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:&quot;Anthropic Sans&quot;, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, sans-serif;font-size:16px;font-weight:400;text-anchor:start;dominant-baseline:auto"/>
<text x="620" y="524" text-anchor="middle" font-family="monospace" font-size="8" fill="#AAFFAA" style="fill:rgb(170, 255, 170);stroke:none;color:rgb(255, 255, 255);stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;opacity:1;font-family:monospace;font-size:8px;font-weight:400;text-anchor:middle;dominant-baseline:auto">Simulation Mode</text>
</svg>

### Plan d'adressage IP

| VLAN | Nom | Réseau | Passerelle | Plage utilisable |
|------|-----|--------|------------|------------------|
| 10 | Direction | 192.168.10.0/24 | 192.168.10.1 | .2 → .254 |
| 20 | Informatique | 192.168.20.0/24 | 192.168.20.1 | .2 → .254 |
| 30 | Utilisateurs | 192.168.30.0/24 | 192.168.30.1 | .2 → .254 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 | .2 → .254 |

---

## ⚙️ Configuration réseau

### Routeur R1 — Router-on-a-Stick

```cisco
! Activation de l'interface physique
interface GigabitEthernet0/0
 no shutdown

! Sous-interface VLAN 10 (Direction)
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 description VLAN_Direction

! Sous-interface VLAN 20 (Informatique)
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 description VLAN_Informatique

! Sous-interface VLAN 30 (Utilisateurs)
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 description VLAN_Utilisateurs

! Sous-interface VLAN 99 (Management)
interface GigabitEthernet0/0.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
 description VLAN_Management
```

### Switch Core SW-CORE — Configuration des VLANs

```cisco
! Création des VLANs
vlan 10
 name Direction
vlan 20
 name Informatique
vlan 30
 name Utilisateurs
vlan 99
 name Management

! Port trunk vers le routeur R1
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 switchport trunk native vlan 99
 description Trunk_vers_R1

! Port trunk vers les switches d'accès
interface GigabitEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 description Trunk_vers_SW-ACC1
```

### Switch d'accès SW-ACC1 — Ports utilisateurs

```cisco
! Ports accès VLAN 10 (Direction)
interface range FastEthernet0/1-5
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 description Postes_Direction

! Port trunk vers SW-CORE
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 description Trunk_vers_SW-CORE
```

---

## 🛠 Rôle de GLPI dans ce scénario

### Qu'est-ce que GLPI ?

**GLPI** (Gestionnaire Libre de Parc Informatique) est un outil ITSM open-source permettant de gérer l'ensemble du patrimoine informatique d'une organisation : inventaire, helpdesk, gestion des changements, contrats, et supervision.

### Apport de GLPI dans cette topologie

#### 1. Inventaire automatisé du parc

Dans cette topologie à 3 VLANs, l'agent GLPI peut être déployé sur **chaque poste utilisateur** (VLAN 10, 20, 30) et remonter automatiquement :

- Le matériel : CPU, RAM, disques, carte réseau, adresse MAC
- Le logiciel : OS, applications installées, licences
- La localisation réseau : IP, VLAN d'appartenance, switch connecté

```
[Poste VLAN 10] ──agent──▶ [Serveur GLPI VLAN 20] ──▶ [Base de données]
[Poste VLAN 30] ──agent──▶         ↑
                         [Collecte via FusionInventory/Agent]
```

#### 2. Gestion des incidents (Helpdesk)

GLPI permet aux utilisateurs de **créer des tickets d'incidents** depuis leur navigateur. Dans ce scénario :

| VLAN source | Type d'incident typique | Priorité suggérée |
|-------------|------------------------|-------------------|
| VLAN 10 (Direction) | Accès VPN, messagerie | Haute |
| VLAN 20 (Informatique) | Panne serveur, mise à jour | Critique |
| VLAN 30 (Utilisateurs) | Imprimante, logiciel | Moyenne |

#### 3. Gestion des changements

Tout changement de configuration réseau (ajout d'un VLAN, modification d'ACL) peut être **tracé dans GLPI** via le module de gestion des changements, assurant une traçabilité conforme aux normes ITIL.

#### 4. Limites de GLPI dans ce contexte

- ❌ GLPI ne supervise pas en temps réel le réseau (pas de monitoring actif) → compléter avec **Zabbix**
- ❌ GLPI ne détecte pas automatiquement les intrusions → compléter avec un **IDS/IPS**
- ❌ L'agent GLPI nécessite une connectivité réseau entre les VLANs → nécessite des **ACLs permissives** vers le serveur GLPI

---

## 🚨 Incidents potentiels

### Incidents réseau

| ID | Incident | VLAN concerné | Impact | Probabilité |
|----|----------|--------------|--------|-------------|
| INC-01 | Boucle réseau (STP défaillant) | Tous | Critique — réseau down | Moyenne |
| INC-02 | VLAN Hopping (double encapsulation 802.1Q) | Tous | Critique — accès non autorisé | Faible |
| INC-03 | Saturation du lien trunk | SW-CORE ↔ R1 | Haute — dégradation service | Moyenne |
| INC-04 | Panne du routeur R1 (SPOF) | Tous | Critique — plus de routage inter-VLAN | Faible |
| INC-05 | Usurpation DHCP (Rogue DHCP Server) | VLAN 30 | Haute — mauvaise attribution d'IP | Moyenne |
| INC-06 | ARP Spoofing / Poisoning | Tous | Haute — interception de trafic | Moyenne |
| INC-07 | Accès non autorisé entre VLANs | VLAN 10 ↔ 30 | Haute — fuite de données | Faible |

### Incidents liés au parc informatique

| ID | Incident | Actif concerné | Impact |
|----|----------|---------------|--------|
| INC-08 | Poste utilisateur infecté par ransomware | VLAN 30 | Critique |
| INC-09 | Licence logicielle expirée | Tous postes | Moyenne |
| INC-10 | Disque dur défaillant sur serveur GLPI | Serveur VLAN 20 | Critique |
| INC-11 | Mot de passe administrateur compromis | Infrastructure | Critique |

---

## 🔓 Vulnérabilités et problèmes sous-jacents

### V1 — VLAN Natif non sécurisé

**Problème** : Si le VLAN natif du trunk est le VLAN 1 (défaut Cisco), un attaquant peut envoyer des trames sans tag et accéder à tous les VLANs.

```
Attaquant [VLAN 1] ──double tag 802.1Q──▶ trafic rebondit vers VLAN cible
```

**Impact** : Accès non autorisé à des VLANs sensibles (VLAN 10 Direction).

---

### V2 — Absence de DHCP Snooping

**Problème** : N'importe quel hôte du réseau peut se faire passer pour un serveur DHCP légitime et distribuer de fausses adresses IP / passerelles.

**Impact** : Redirection du trafic vers un hôte malveillant (attaque Man-in-the-Middle).

---

### V3 — Absence de Dynamic ARP Inspection (DAI)

**Problème** : Sans DAI, un attaquant peut envoyer de fausses réponses ARP et associer son adresse MAC à l'IP de la passerelle.

**Impact** : Interception de tout le trafic du segment réseau (ARP Poisoning).

---

### V4 — Ports inutilisés actifs sur les switches

**Problème** : Les ports non utilisés restent actifs et dans le VLAN par défaut (VLAN 1), permettant à un attaquant de brancher un équipement non autorisé.

**Impact** : Connexion physique non contrôlée au réseau.

---

### V5 — Absence d'authentification 802.1X

**Problème** : N'importe quel équipement branché physiquement sur un port switch accède immédiatement au réseau sans authentification.

**Impact** : Accès réseau non contrôlé.

---

### V6 — Point de défaillance unique (SPOF) sur R1

**Problème** : Le routeur R1 assure seul le routage inter-VLAN. Sa panne interrompt toute communication entre VLANs.

**Impact** : Indisponibilité totale des services inter-VLANs.

---

## ✅ Corrections et bonnes pratiques

### Correction V1 — Sécuriser le VLAN natif

```cisco
! Changer le VLAN natif vers un VLAN dédié inutilisé
interface GigabitEthernet0/1
 switchport trunk native vlan 99

! Désactiver le VLAN 1 sur tous les trunks
switchport trunk allowed vlan remove 1
```

### Correction V2 — Activer le DHCP Snooping

```cisco
! Activer globalement
ip dhcp snooping
ip dhcp snooping vlan 10,20,30

! Désactiver l'option 82 (évite les problèmes avec certains DHCP)
no ip dhcp snooping information option

! Marquer le port vers le DHCP légitime comme trusted
interface GigabitEthernet0/1
 ip dhcp snooping trust
```

### Correction V3 — Activer Dynamic ARP Inspection

```cisco
! Activer DAI sur les VLANs
ip arp inspection vlan 10,20,30

! Port trusted (vers le routeur / DHCP)
interface GigabitEthernet0/1
 ip arp inspection trust
```

### Correction V4 — Désactiver les ports inutilisés

```cisco
! Désactiver tous les ports inutilisés et les isoler dans un VLAN black-hole
interface range FastEthernet0/10-24
 switchport mode access
 switchport access vlan 99
 shutdown
 description PORT_INUTILISE
```

### Correction V5 — Activer PortFast + BPDU Guard sur les ports utilisateurs

```cisco
interface range FastEthernet0/1-9
 spanning-tree portfast
 spanning-tree bpduguard enable
```

> 💡 Pour une sécurité maximale, implémenter **802.1X** avec un serveur **RADIUS** (ex: FreeRADIUS) pour l'authentification des équipements.

### Correction V6 — Redondance avec HSRP

```cisco
! Routeur principal R1
interface GigabitEthernet0/0.10
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt

! Routeur secondaire R2
interface GigabitEthernet0/0.10
 standby 10 ip 192.168.10.1
 standby 10 priority 100
```

---

## 🔧 Changements de configuration recommandés

| Priorité | Changement | Risque mitigé | Complexité |
|----------|-----------|--------------|------------|
| 🔴 Critique | Activer DHCP Snooping | Rogue DHCP | Faible |
| 🔴 Critique | Sécuriser le VLAN natif | VLAN Hopping | Faible |
| 🔴 Critique | Désactiver les ports inutilisés | Accès physique non autorisé | Faible |
| 🟠 Haute | Activer DAI | ARP Spoofing | Moyenne |
| 🟠 Haute | Implémenter HSRP sur R1/R2 | SPOF | Moyenne |
| 🟡 Moyenne | Déployer 802.1X + RADIUS | Accès non authentifié | Haute |
| 🟡 Moyenne | ACLs inter-VLANs | Mouvements latéraux | Moyenne |
| 🟢 Faible | Activer SSH v2 (désactiver Telnet) | Écoute réseau | Faible |

### ACLs inter-VLANs recommandées

```cisco
! Interdire au VLAN 30 (Utilisateurs) d'accéder au VLAN 10 (Direction)
ip access-list extended ACL_VLAN30_TO_VLAN10
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.30
 ip access-group ACL_VLAN30_TO_VLAN10 in
```

---

## 🌍 Normes internationales appliquées

### ISO/IEC 27001 — Sécurité de l'information

| Contrôle | Application dans cette topologie |
|---------|----------------------------------|
| A.9 — Contrôle d'accès | VLANs séparant les profils utilisateurs, ACLs inter-VLANs |
| A.12 — Sécurité opérationnelle | Journalisation des événements réseau, GLPI pour la traçabilité |
| A.13 — Sécurité des communications | Segmentation réseau, chiffrement des flux de management (SSH) |
| A.16 — Gestion des incidents | Processus de ticketing GLPI, classification des incidents |

### ITIL v4 — Gestion des services IT

| Pratique ITIL | Mise en œuvre |
|--------------|---------------|
| Gestion des incidents | Tickets GLPI avec SLA par VLAN/profil |
| Gestion des changements | Traçabilité des modifs réseau dans GLPI |
| Gestion des actifs | Inventaire automatisé via agent GLPI |
| Gestion des problèmes | Analyse des incidents récurrents dans GLPI |

### IEEE 802.1Q — Standard VLANs

- Encapsulation dot1Q sur les liens trunk
- Segmentation logique du réseau en 4 VLANs distincts
- VLAN natif dédié (VLAN 99) conformément aux bonnes pratiques

### IEEE 802.1D / 802.1w — Spanning Tree Protocol

- STP activé pour prévenir les boucles réseau
- RSTP (802.1w) recommandé pour une convergence plus rapide
- PortFast + BPDU Guard sur les ports utilisateurs finaux

### RGPD — Règlement Général sur la Protection des Données

| Obligation RGPD | Mesure technique appliquée |
|----------------|---------------------------|
| Minimisation des données | Accès segmenté par VLAN selon le rôle |
| Traçabilité | Journaux GLPI horodatés |
| Intégrité | ACLs limitant les accès inter-VLANs |
| Disponibilité | Redondance HSRP sur le routeur |

---

## 📚 Ressources et outils utilisés

- **Cisco Packet Tracer** — Simulation de la topologie réseau
- **GLPI** — Gestion du parc et helpdesk (ITSM)
- **Zabbix** — Supervision réseau en temps réel
- **Docker** — Conteneurisation du service GLPI

---

## 👤 Auteur

TIAKRAY MANGLE Jean Marc Riad  
Formation : Gestion du Parc Informatique et Incident  
Formateur : Boris Rose  

---

*Ce dépôt s'inscrit dans le cadre du portfolio obligatoire de la formation.*
