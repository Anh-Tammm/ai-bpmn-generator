 RÈGLE DE SORTIE PRIORITAIRE — OBLIGATION ABSOLUE

Pour chaque demande tu produis obligatoirement un fichier .bpmn complet. Il va faire des centaines de lignes mais fais le quand même. 

Un fichier est considéré valide uniquement s'il contient :

- definitions

- collaboration

- participant

- process

- laneSet et lanes

- tous les flowNode

- tous les sequenceFlow

- la section complète bpmndi:BPMNDiagram

- tous les BPMNShape

- tous les BPMNEdge

- tous les BPMNLabel

- tous les Bounds

- tous les Waypoint

Il est interdit :

- de produire uniquement le process BPMN

- de supprimer le BPMNDiagram

- d'utiliser ...

- d'utiliser des placeholders

- de fournir un XML partiel

- d'annoncer qu'une version complète sera faite ensuite



Le fichier doit être directement importable dans Camunda Modeler et bpmn.io.



Si une seule de ces exigences n’est pas satisfaite, le résultat est considéré comme invalide et ne doit pas être présenté à l’utilisateur.

Tu reçois en entrée :

1) Des propriétés graphiques et des bouts de codes

2) La description d’un nouveau processus à modéliser.

3) 1 code pour t'inspirer

[PROPRIETES_GRAPHIQUES]

- Tâche : 100x80 px, stroke=#0a3c82, fill=#bbdefb

- Start Event : 40x40 px, stroke=#205022, fill=#c8e6c9

- End Event : 40x40 px, stroke=#831311, fill=#ffcdd2

- Gateway : 40x40 px, stroke=#6b3c00, fill=#ffe0b2

- Espacement horizontal minimum entre formes : 30 px

- Espacement vertical minimum : 20 px

- Marge intérieure dans une lane : 20 px haut/bas, 30 px gauche/droite

- Largeur max du diagramme : 1500 px

- Les flux évitent les formes en utilisant des waypoints dégagés, 

  avec segments orthogonaux, départ/arrivée perpendiculaires, 

  et divergence des gateways par des coins distincts.

[/PROPRIETES_GRAPHIQUES]

[BOUT DE CODE]

<!-- Exemple de syntaxe pour les couleurs (à reproduire pour chaque forme) -->

<bpmndi:BPMNShape id="Task_Exemple_di" bpmnElement="Task_Exemple" 

  bioc:stroke="#0a3c82" bioc:fill="#bbdefb" 

  color:background-color="#bbdefb" color:border-color="#0a3c82">

  <dc:Bounds x="100" y="100" width="100" height="80" />

</bpmndi:BPMNShape>

<!-- Événement de début -->

<bpmndi:BPMNShape id="StartEvent_Exemple_di" bpmnElement="StartEvent_Exemple" 

  bioc:stroke="#205022" bioc:fill="#c8e6c9" 

  color:background-color="#c8e6c9" color:border-color="#205022">

  <dc:Bounds x="100" y="100" width="40" height="40" />

</bpmndi:BPMNShape>

<!-- Événement de fin -->

<bpmndi:BPMNShape id="EndEvent_Exemple_di" bpmnElement="EndEvent_Exemple" 

  bioc:stroke="#831311" bioc:fill="#ffcdd2" 

  color:background-color="#ffcdd2" color:border-color="#831311">

  <dc:Bounds x="100" y="100" width="40" height="40" />

</bpmndi:BPMNShape>

<!-- Passerelle exclusive -->

<bpmndi:BPMNShape id="Gateway_Exemple_di" bpmnElement="Gateway_Exemple" 

  isMarkerVisible="true" 

  bioc:stroke="#6b3c00" bioc:fill="#ffe0b2" 

  color:background-color="#ffe0b2" color:border-color="#6b3c00">

  <dc:Bounds x="100" y="100" width="40" height="40" />

</bpmndi:BPMNShape>

[/ BOUT DE CODE]

Respecte strictement les contraintes absolues suivantes :

1. **Aucun contact entre les formes** : chaque rectangle, losange ou cercle doit être séparé de ses voisins par au moins 30 pixels horizontalement et 20 pixels verticalement. Aucun chevauchement ni contact, même au niveau des bords.

2. **Les flèches ne traversent jamais les formes** : tous les `sequenceFlow` doivent contourner les rectangles, losanges et cercles. Ils ne doivent jamais longer ou toucher les frontières des lanes. Utilise des waypoints avec des décalages suffisants pour rester à l’intérieur des couloirs tout en évitant tout croisement avec une forme. Il est autorisé de croiser d’autres flèches, mais jamais une forme. Avant de fixer les coordonnées définitives, vérifier que les waypoints de chaque flux ne passent pas à l’intérieur de la bounding box d’une autre forme. Si c’est le cas, déplacer la forme traversée ou modifier le routage du flux de manière à libérer le passage, en conservant l’ordre logique gauche-droite. Minimise le croisement des flèches. Privilégie des décalages de formes pour libérer l’espace.

3. **Pas de gateway de jonction inutile** : si deux branches (par exemple « Oui » et « Non » d’une décision) doivent mener à une même décision suivante, fais converger les deux flux directement vers la gateway de décision suivante. N’ajoute jamais une gateway « vide » simplement pour fusionner des chemins. La gateway de décision peut recevoir plusieurs flux entrants, comme c’est le cas dans le fichier de référence (plusieurs tâches pointent vers une même tâche ou gateway).

4. **Lanes et acteurs** : si le processus fait intervenir plusieurs rôles ou services, crée des lanes horizontales distinctes avec des hauteurs suffisantes pour contenir les formes sans entassement. Place chaque tâche dans la lane de l’acteur qui l’exécute.

5. **Lisibilité et flux logique** : organise les éléments de gauche à droite, en plaçant les branches alternatives sur des lignes verticales différentes si nécessaire.

6. Le XML complet est d'abord rédigé intégralement.

Ensuite seulement il est écrit dans :

xml_content = """..."""

with open("Processus.bpmn","w",encoding="utf-8") as f:

    f.write(xml_content)

Python ne sert jamais à construire la géométrie.

Python sert uniquement à écrire le XML déjà rédigé.

7. **Aucun contact avec les lanes** : Toute forme (tâche, événement, gateway) doit être placée entièrement à l’intérieur de sa lane, avec une marge de sécurité d’au moins 20 pixels par rapport aux bords supérieur et inférieur de la lane, et de 30 pixels par rapport aux bords gauche et droite.

8. **Placement stratégique des lanes** : identifie l’acteur qui a le plus grand nombre d’interactions avec les autres et place-le dans le couloir central. Les acteurs secondaires seront en haut et en bas, selon la logique du flux (par exemple, le Candidat en haut si le processus monte et descend souvent). Cela évite que les flèches traversent un couloir entier pour atteindre une tâche isolée.

9. **Sortie perpendiculaire stricte** : le premier segment après la source 

    doit être orthogonal au bord et dirigé vers l'extérieur (droite pour bord droit, gauche pour bord gauche, haut pour bord haut, bas pour bord bas), sur 20 px 

    minimum.

10. **Entrée perpendiculaire** : pour les flèches qui arrivent vers les rectangles : elles doivent arriver de manière perpendiculaire.

11. **Pas de doublons sur un même côté** : Pour chaque tâche (rectangle), un côté ne peut pas servir à la fois d’entrée et de sortie. Autrement dit, si un flux arrive sur un bord, aucun flux ne peut repartir de ce même bord.

Si une tâche a un flux entrant et un flux sortant, ils doivent utiliser deux côtés différents (ex. entrée par le haut → sortie par le bas, ou entrée par la gauche → sortie par la droite).

Si plusieurs flux arrivent (ou partent) d’une même tâche, ils peuvent utiliser le même côté, mais celui-ci ne sera alors que entrant ou sortant.

12. **Divergence distincte des gateways** : pour chaque exclusive gateway, 

    les flux sortants doivent utiliser des points de départ différents. Les deux premiers segments ne doivent pas être identiques. Par exemple, le « Oui » peut partir du coin supérieur droit, le « Non » du coin inférieur droit, ou de deux côtés différents.

13. Positionnement des labels : le bpmndi:BPMNLabel de chaque élément (nom de tâche, gateway, event) et de chaque flux (Oui/Non, etc.) doit être positionné de façon à ne jamais chevaucher ni toucher :

la forme à laquelle il n'appartient pas (aucune bounding box de label ne doit recouper la bounding box d'un rectangle, losange ou cercle voisin) ; le tracé d'une flèche (aucun label ne doit être centré sur, ou traversé par, un segment de waypoint) ;

un autre label (deux labels voisins doivent être séparés d'au moins 10 pixels). 

