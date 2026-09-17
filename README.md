Jeu d'échecs Python — README
1. Description du projet
Ce projet est un jeu d'échecs jouable en interface graphique (via pygame), où un joueur humain affronte une intelligence artificielle. Le plateau est dessiné en SVG puis converti en image via cairosvg, et toute la logique des règles du jeu (coups légaux, échec, mat, etc.) est gérée par la bibliothèque python-chess.

Les Bibliothèques utilisées
chess	Gère les règles du jeu (plateau, coups légaux, détection de fin de partie)
chess.svg	Génère une représentation visuelle du plateau au format SVG
cairosvg	Convertit le SVG du plateau en image PNG affichable
pygame	Crée la fenêtre graphique, affiche l'image et gère les clics souris
2. Structure du code
evaluat(board) -> float
Fonction d'évaluation d'une position d'échecs. Retourne :
1.0 si les Blancs ont gagné
-1.0 si les Noirs ont gagné
0.0 en cas de nulle, ou si la partie n'est pas terminée (valeur neutre par défaut, à compléter avec une vraie heuristique — matériel, position, mobilité, etc.)

searchBestMove(board) -> chess.Move
Fonction destinée à contenir l'algorithme de décision de l'IA (minimax / alpha-bêta). Version actuelle : placeholder — elle choisit un coup légal au hasard parmi tous les coups possibles. C'est la partie à développer pour donner une vraie stratégie à l'IA.

Boucle principale (if __name__ == "__main__":)
Initialise le plateau (chess.Board()) et la fenêtre pygame (768×768 px, soit 96 px par case).
Tour de l'IA : si ce n'est pas au joueur humain de jouer, searchBestMove est appelée et son coup est joué.

Gestion des clics souris :
1er clic sur une pièce du joueur → sélectionne la case et surligne les coups légaux possibles.
2e clic sur une case destination → joue le coup s'il est légal.
Affichage : le plateau est régénéré à chaque tour de boucle (SVG → PNG → image pygame), avec surlignage du dernier coup et des cases sélectionnées.
Fin de partie : un message ("Les blancs gagnent" / "Les noirs gagnent" / "Partie nulle") s'affiche au centre de l'écran.

4. Comment faire fonctionner le projet (installation pas à pas)
Voici, dans l'ordre, les problèmes rencontrés et comment ils ont été résolus.

Étape 1 — Python n'était pas installé
La commande python renvoyait une redirection vers le Microsoft Store au lieu de lancer Python. Cause : Python n'était pas réellement installé sur la machine.
Solution : téléchargement et installation de Python (dernière version, 3.14.7) depuis python.org, avec le nouveau Python install manager, qui a aussi proposé et corrigé automatiquement :
les alias d'exécution Windows (pour que python et py pointent vers le vrai interpréteur)
l'activation du support des chemins longs (>260 caractères), nécessaire pour certains paquets

Étape 2 — pygame ne s'installait pas avec Python 3.14
pip install pygame échouait en tentant de compiler pygame depuis les sources (ModuleNotFoundError: No module named 'distutils.msvccompiler'), car pygame n'a pas encore de version précompilée (wheel) pour Python 3.14, qui est trop récent.
Solution : installation d'une version plus ancienne et stable, Python 3.12, en parallèle de la 3.14, via le gestionnaire py :
powershell
py install 3.12

Puis création d'un environnement virtuel dédié au projet, basé sur cette version :
powershell
py -3.12 -m venv venv
.\venv\Scripts\activate
pip install chess cairosvg pygame

Cette fois, pip a trouvé un wheel précompilé (pygame-2.6.1-cp312-cp312-win_amd64.whl) et l'installation s'est faite sans compilation.


Étape 4 — DLL Cairo manquante
cairosvg dépend de la bibliothèque système Cairo (écrite en C) via cairocffi. Cette bibliothèque n'est pas installée avec pip install cairosvg — seul le wrapper Python l'est (OSError: no library called "cairo-2" was found).
Solution : installation du GTK3 Runtime pour Windows, qui fournit les DLL Cairo nécessaires et les ajoute au PATH système : GTK3 Runtime — GitHub Releases

Étape 5 — Environnement virtuel désactivé après redémarrage
Après avoir redémarré VS Code (nécessaire pour prendre en compte le nouveau PATH de GTK3), l'environnement virtuel n'était plus actif, provoquant à nouveau ModuleNotFoundError: No module named 'chess'.
Solution : réactiver l'environnement avant chaque lancement du script :
powershell
.\venv\Scripts\activate
python jeu_echecs_commente.py
