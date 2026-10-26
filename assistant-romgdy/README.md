# Assistant IA personnel & stratégique — ROMGDY

Système prêt à installer dans l'app **Claude sur iPhone**. Aucun serveur à héberger, aucun code à maintenir.

---

## 1. Architecture retenue (et pourquoi)

**Un seul agent central, deux compétences spécialisées.**

```
                ┌──────────────────────────────────────────┐
  iPhone        │  Projet Claude « ROMGDY HQ »              │
  (voix, photo, │  ─ Instructions du projet (cerveau)       │
   capture,     │  ─ Connaissances : profil, tarifs, style  │
   lien)  ────► │  ─ Mémoire du projet (apprend, éditable)  │
                │                                           │
                │   Skill 1 : romgdy-emails                 │──► Gmail (lecture + BROUILLONS)
                │   Skill 2 : romgdy-stratege               │──► Recherche web / Recherche approfondie
                └──────────────────────────────────────────┘
```

Pourquoi pas deux agents séparés : tu veux passer « d'une musique de film à une négociation, puis à une tenue de shooting » sans changer d'outil. Un seul agent partage la même mémoire. Il sait par exemple qu'une marque qui t'écrit par mail correspond à l'esthétique dont vous avez parlé hier. Les deux *skills* se déclenchent tout seuls selon la demande : il n'y a rien à choisir.

| Besoin du cahier des charges | Comment c'est couvert |
|---|---|
| Utilisation sur iPhone, à la voix | App Claude iOS → mode vocal (icône onde sonore) ou dictée |
| Photos, vidéos, captures, liens | Pièce jointe / partage iOS « Envoyer vers Claude » |
| Lecture et analyse des e-mails | Connecteur **Gmail** (déjà connecté à ton compte) |
| « Je vérifie et j'appuie sur Envoyer » | L'agent crée des **brouillons Gmail** ; tu les ouvres dans l'app Gmail et tu envoies |
| Aucun envoi sans validation | Règle absolue dans le skill. L'outil d'envoi n'est jamais utilisé |
| Récap automatique chaque soir | Tâche planifiée Claude (optionnelle, voir §4) |
| Tendances actualisées et sources | Recherche web + Recherche approfondie, sources listées dans `skills/romgdy-stratege/references/sources-tendances.md` |
| Mémoire consultable, modifiable, supprimable | Mémoire du Projet (Réglages → Mémoire) + fichier `knowledge/profil-romgdy.md` |
| Questionnaire initial | `knowledge/questionnaire.md`, lancé automatiquement au premier usage |
| Conseils sincères, pas de flatterie | Règle inscrite dans les instructions du projet |

---

## 2. Installation (≈ 15 minutes, une seule fois)

> Le plus simple est de le faire depuis **claude.ai sur ordinateur**. Tout se synchronise ensuite sur l'iPhone.
> Un abonnement **Pro ou Max** est recommandé (Projets, skills, recherche approfondie, mémoire, connecteurs).

1. **Activer les capacités** : Réglages → Capacités → activer *Recherche web*, *Mémoire*, *Skills* (ainsi que *Exécution de code et création de fichiers*, nécessaire aux skills).
2. **Vérifier Gmail** : Réglages → Connecteurs → Gmail doit apparaître comme connecté.
3. **Installer les deux skills** : Réglages → Capacités → Skills → *Importer un skill* → importer `romgdy-emails.zip` puis `romgdy-stratege.zip` (fichiers dans `dist/`).
4. **Créer le Projet** « ROMGDY HQ » :
   - *Instructions* : copier-coller le contenu de `project-instructions.md`.
   - *Connaissances* : ajouter `knowledge/profil-romgdy.md` et `knowledge/questionnaire.md`.
5. **Sur l'iPhone** : ouvrir l'app Claude → Projets → ROMGDY HQ → épingler. Toutes les conversations se lancent depuis ce projet.
6. **Premier message** : « On commence l'onboarding. » L'agent te pose le questionnaire bloc par bloc, à l'écrit ou à la voix, puis te propose un profil mis à jour à remplacer dans les Connaissances.

---

## 3. Utilisation au quotidien

Parle-lui naturellement, aucune commande n'est nécessaire :

- « Fais-moi le récap de mes mails d'aujourd'hui, classe les propositions et prépare les réponses. »
- « Trouve-moi dix sons qui montent au Brésil cette semaine, explique les paroles et propose-moi des concepts. »
- *(capture d'une offre)* « Ça vaut le coup ? Comment je négocie ? »
- *(photo de 3 tenues)* « Shooting ciné sombre jeudi, laquelle et pourquoi ? »
- « Retiens que je ne veux plus travailler avec des marques de crypto. »
- « Qu'est-ce que tu sais sur mes négociations en cours ? » / « Oublie ce que je t'ai dit sur X. »

---

## 4. Récap e-mail automatique chaque soir (optionnel)

Je peux programmer une tâche qui tourne chaque soir (par ex. 20 h 30, heure de Paris). Elle lit les mails du jour, prépare les brouillons dans Gmail et t'envoie une notification avec le récap. Elle ne fait **jamais** d'envoi. Il suffit de me le demander.

---

## 5. Limites honnêtes et solutions de contournement

| Limite | Contournement |
|---|---|
| Pas d'accès API direct à tes **statistiques TikTok / Instagram** | Envoie des captures de *TikTok Studio* et *Instagram Insights* (vues, rétention, partages, audience par pays). L'agent les lit et les compare dans le temps. Un accès automatisé passerait par l'API Meta Graph et l'API TikTok for Business, qui demandent un développement dédié (phase 2 possible). |
| Les plateformes ne publient pas de « tendances en temps réel » ouvertes | L'agent croise TikTok Creative Center (tendances par pays), les charts Spotify/Shazam Viral, Kworb, Tokchart, la presse spécialisée et les signaux faibles (voir la liste des sources). Il date chaque info et indique son niveau de confiance. |
| Lecture de vidéo | Envoie la vidéo ou des captures clés : l'agent analyse les images, le rythme visible, le texte à l'écran et la légende. Pour l'audio, donne le nom du son. |
| Mémoire des projets | La mémoire est propre au projet ROMGDY HQ : c'est voulu, elle reste séparée de tes autres conversations. Tu la consultes et tu la modifies dans Réglages. |

## 6. Confidentialité

- Le connecteur Gmail n'a que les droits que tu lui as accordés. Tu peux le révoquer à tout moment dans Réglages → Connecteurs.
- Les données des projets ne servent pas à entraîner les modèles, sauf si tu l'as activé (Réglages → Confidentialité).
- L'agent ne transmet jamais tes tarifs ni tes contrats à un tiers, et ne met jamais ton tarif plancher dans un brouillon.
