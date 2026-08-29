# Text-to-BPMN : Générateur de Processus par IA avec Custom GPT

## Contexte métier
La cartographie des processus selon la norme stricte BPMN (Business Process Model and Notation) est une tâche manuelle extrêmement chronophage sur des outils comme Visio. Pourtant, elle est vitale pour la conformité réglementaire et les audits de sécurité, particulièrement pour les ingénieurs Qualité dans le secteur ferroviaire. 

Ce projet vise à automatiser la création de ces logigrammes : l'utilisateur décrit son processus en langage naturel, et l'outil génère un fichier `.bpmn` normé, stylisé et prêt à être importé.

**Impact métier :**
* **Productivité :** Réduction du temps de modélisation de plusieurs heures à quelques minutes.
* **Conformité :** Standardisation automatique des schémas d'entreprise pour les audits de sécurité.

## STechnique
* **IA Générative :** Custom GPT 
* **Ingénierie :** Prompt Engineering avancé (Spatial Reasoning, Constraint Satisfaction)
* **Langages :** XML (Structure BPMN 2.0)
* **Standards :** Camunda Modeler, bpmn.io

## Défis techniques : "Spatial Reasoning" par LLM
Le défi majeur de ce projet est structurel : **les LLM  n'ont aucune notion de l'espace ni de la géométrie.** Si on leur demande un schéma, ils superposent les formes ou font traverser les flèches en plein milieu des tâches. 

Pour résoudre ce problème, j'ai développé un **Prompt avec beaucoup de contraintes** forçant l'IA à agir comme un algorithme d'optimisation géométrique :

### 1. Résolution spatiale et géométrique
L'IA est contrainte de calculer mentalement des coordonnées `X Y` et des *Bounding Boxes* :
* Maintien d'un espacement strict (30px horizontal, 20px vertical) entre toutes les formes.
* Obligation de placer les acteurs clés au centre pour minimiser la distance des flux de données.

### 2. Routage orthogonal sans collision
Pour éviter les croisements chaotique, le prompt dicte un routage algorithmique :
* Les flèches ne doivent **jamais** traverser une forme.
* Les sorties doivent être strictement perpendiculaires sur au moins 20 pixels.
* Les divergence (par exemple bloc oui/non) doivent utiliser des points de départ physiquement distincts sur le losange.

### 3. Génération de Code
Le prompt interdit à l'IA d'utiliser des générateurs de code tiers pour la géométrie. Elle doit rédiger nativement un fichier XML valide contenant l'intégralité des balises (`bpmndi:BPMNDiagram`, `BPMNShape`, `Waypoint`), en injectant des templates de design (tailles fixes, codes couleurs hexadécimaux).

## Extrait du Prompt de Contrainte 

```text
RÈGLE DE SORTIE PRIORITAIRE — OBLIGATION ABSOLUE
[...]
1. Aucun contact entre les formes : chaque rectangle, losange ou cercle doit être séparé de ses voisins par au moins 30 px horizontalement et 20 px verticalement.
2. Les flèches ne traversent jamais les formes : tous les `sequenceFlow` doivent contourner les rectangles, losanges et cercles. Utilise des waypoints avec des décalages suffisants.
[...]
9. Sortie perpendiculaire stricte : le premier segment après la source doit être orthogonal au bord et dirigé vers l'extérieur sur 20 px minimum.
11. Pas de doublons sur un même côté : Pour chaque tâche (rectangle), un côté ne peut pas servir à la fois d’entrée et de sortie.
