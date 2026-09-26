---
name: project-talesens
description: "Projet TALESENS — site vitrine Astro 5 pages déployé en double (prod OVH 51.77.203.189 + miroir homelab via Cloudflare Tunnel) pour l'agence de comm/storytelling/brand des fondatrices Douâae Tsouli Kamal & Sofia Khalil El Ouadghiri"
metadata: 
  node_type: memory
  type: project
  originSessionId: ce3df7b7-ee96-4a98-a818-8bcc365cfc11
---

TALESENS = agence de conseil en communication, storytelling et stratégie de marque (fondatrices : Douâae Tsouli Kamal & Sofia Khalil El Ouadghiri, toutes deux INJAZ Al-Maghrib). Site vitrine 5 pages : Accueil, Qui sommes-nous, Services, Références, Contact.

**Stack** : Astro 6 statique (Node 22+ requis). Repo unique : github.com/adilinfo14/TALESENS.git, branche main.

**Brand book** : couleur principale violet électrique #6A5CFF, accents rose néon #FF4D8D + bleu digital #00B4FF, fonds midnight #0D0F1D et blanc doux #F7F7FA. Typos Poppins (titres) + Inter (texte) via Google Fonts. Ton humain, stratégique, inspirant, créatif, moderne, émotionnel.

**Déploiement double (rituel) :**

1. **OVH (prod publique, 51.77.203.189)** : repo cloné dans `/home/ubuntu/talesens/`. Build Node 22 via nvm (chargé par `export NVM_DIR=$HOME/.nvm && source $NVM_DIR/nvm.sh && nvm use 22`). Servi par **nginx natif host** (pas Docker) avec vhost `/etc/nginx/sites-available/talesens` linké en `/etc/nginx/sites-enabled/00-talesens` (préfixe 00 pour primer sur la regex catch-all ConfIA `~^(?<artisan_slug>...)\.noschoixpourvous\.com$`). HTTP plain port 80 — le HTTPS est terminé par Cloudflare en mode proxy/Flexible. Sudo passwordless dispo (ubuntu = root via sudo).

2. **Homelab (miroir, VM `confia-vm`)** : projet dans `/home/adil/talesens/`. Build → `dist/` monté en volume `:ro` dans `proxy-nginx` Docker. Vhost `talesens.conf` dans `/home/proxy/nginx/conf.d/` (écrit via docker exec). Exposé via Cloudflare Tunnel (cf [[project-confia-cloudflare-tunnel]]).

**DNS (Cloudflare)** : `talesens.noschoixpourvous.com` doit pointer vers OVH en prod — soit en **A record 51.77.203.189 proxied (orange-cloud)**, soit via la route Cloudflare Tunnel vers homelab (à choisir, pas les deux à la fois). Le HTTPS est fourni par Cloudflare dans les deux cas.

**Why** : Demande explicite d'Adil — livrer un beau site vitrine premium éditorial à partir du brand book PDF. Le rituel de déploiement [[feedback-rituel-deploiement]] exige OVH + homelab pour toute fonctionnalité.

**How to apply pour modifier le site :**
1. Édition locale (ou direct sur VM/OVH) → git commit + push
2. **Sur OVH** : `ssh ubuntu@51.77.203.189 'cd ~/talesens && git pull && export NVM_DIR=$HOME/.nvm && source $NVM_DIR/nvm.sh && nvm use 22 && npm run build'` (nginx sert automatiquement le nouveau dist, pas besoin de reload).
3. **Sur homelab** : `ssh confia-vm 'cd /home/adil/talesens && git pull && npm run build'` (volume `:ro` sert automatiquement).

Voir aussi [[project-confia]] (homelab), [[project-confia-ovh]] (prod OVH), [[project-confia-cloudflare-tunnel]] (tunnel homelab).
