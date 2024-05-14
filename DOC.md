# AZ Launcher (aka Pactify Launcher)

## Emojis

Le launcher utilise les unicodes standards pour les emojis, mais ils doivent être préfixés du caractère `"\uEEEE"`/`` pour être affichés.
Donc dans des sources Java ça donne: `String s = "\uEEEE😀";` ou `String s = "😀";` (les deux sont équivalents).

Il est possible de récupérer une version à coller directement en faisant un copier spécial depuis le launcher avec `Ctrl+Shift+C`.

## Menus (Inventaires Transparents)

Pour afficher un inventaire comme un menu, il faut l'ouvrir avec un `windowId` >= à 101.
Il n'y a pas d'API Bukkit pour le faire.
Mais c'est possible en NMS ou avec ProtocolLib ([Packet Open Window](https://wiki.vg/index.php?title=Protocol&oldid=7959#Open_Window)).

## ItemStacks Spéciaux

Les propriétés spéciales des ItemStacks sont stockées dans les NBT tags.
Il n'y a pas d'API Bukkit pour les manipuler.
Mais c'est possible en NMS ou avec [NBT API](https://www.spigotmc.org/resources/nbt-api.7939/).

- `PacDisplay` [Compound] - Modifie le rendu des items dans les inventaires.

  - `Sprite` [String] `"PLAY_RIGHT_ARROW"|"PLAY_LEFT_ARROW"|"SERVER_DOWN_ARROW"|"SERVER_UP_ARROW"|"BOOK_NEXT_PAGE"|"BOOK_PREV_PAGE"|"PADLOCK_CLOSE"|"PADLOCK_OPEN"|"WORLD_LENS"|"MAIL"|"MAIL_NEW"|"SIGNAL_RED"|"SIGNAL_GREEN"|"SIGNAL_GRAY"|"BEACON_CHECK"|"BEACON_CROSS"|"EFFECT_MOVESPEED"|"EFFECT_MOVESLOWDOWN"|"EFFECT_DIGSPEED"|"EFFECT_DIGSLOWDOWN"|"EFFECT_DAMAGEBOOST"|"EFFECT_HEAL"|"EFFECT_HARM"|"EFFECT_JUMP"|"EFFECT_CONFUSION"|"EFFECT_REGENERATION"|"EFFECT_RESISTANCE"|"EFFECT_FIRERESISTANCE"|"EFFECT_WATERBREATHING"|"EFFECT_INVISIBILITY"|"EFFECT_BLINDNESS"|"EFFECT_NIGHTVISION"|"EFFECT_HUNGER"|"EFFECT_WEAKNESS"|"EFFECT_POISON"|"EFFECT_WITHER"|"EFFECT_HEALTHBOOST"|"EFFECT_ABSORPTION"|"EFFECT_SATURATION"|"EFFECT_GLOWING"|"EFFECT_LEVITATION"|"EFFECT_LUCK"|"EFFECT_UNLUCK"|"EMOJI"` - Utiliser un sprite qui remplace la texture de l'item
  - `SpriteData` [String] - Si `Sprite="EMOJI"` il s'agit de l'emoji à afficher
  - `Color` [int] - Re-colore la la texture (int au format `0xAARRGGBB`, ex pour du vert pur c'est `0xFF00FF00`, ce qui donne `-16711936`).
  - `TranslatX` [float] - Déplace la texture sur l'axe X.
  - `TranslatY` [float] - Déplace la texture sur l'axe Y.
  - `Rotation` [float] - Fait tourner la texture.
  - `Scale` [float] - Change la taille de la texture (1 = taille normale).
  - `ScaleX` [float] - Change la taille de la texture sur l'axe X (1 = taille normale, seulement si `Scale` n'est pas défini).
  - `ScaleY` [float] - Change la taille de la texture sur l'axe Y (1 = taille normale, seulement si `Scale` n'est pas défini).
  - `ZIndex` [float] - Change l'ordre de rendu de la texture (à n'utiliser que si la texture apparait derrière une autre, etc).
  - `Childs` _Compliqué, ça permet de display plusieurs sprites ou items dans un seul item, voir les exemples_.

- `PacRender` [Compound] - Modifie le rendu des items in-game (pas dans les inventaires)

  - `Color` [int] - Re-colore la texture (int au format 0xAARRGGBB)
  - `Scale` [float] - Change la taille (1 = taille normale)

- `PacMenu` [Compound] - Modifier l'affichage de l'item si l'inventaire est un menu
  - `Background` [boolean] - Afficher ou non le background (la texture de "bouton" cliquable)
  - `State` [string] `"DISABLED"|"NORMAL"|"HOVER"|"ACTIVE"` - Modifier l'état de la texture du background

Quelques exemples de commandes pour générer des items avec ces tags:

```
/give @p minecraft:iron_sword 1 0 {PacRender: {Scale: 2, Color: -65536}, PacDisplay: {Rotation: 90, Color: -65536}}
/give @p minecraft:stone 1 0 {PacDisplay: {Sprite: "EMOJI", SpriteData: "😀"}}
/give @p minecraft:​bow 1 0 {PacDisplay: {Childs: [{id: "minecraft:stone", tag: {PacDisplay: {Sprite: "EFFECT_MOVESPEED", Scale: 0.5, TranslatX: 4, TranslatY: 4}}}]}}
```

## PLSP Protocol

Le PLSP (Pactify Launcher Simple Protocol) est l'une des API disponible publiquement pour communiquer avec le launcher.
Il y a un exemple d'utilisation dans ce repo: https://github.com/PactifyLauncherExamples/PactifyPlugin.

https://github.com/PactifyLauncherExamples/maven-repository/tree/master/pactify/client/api/plsp-protocol/20230914
```
<repositories>
  <repository>
    <id>pactifylauncherexamples-repo</id>
    <url>https://raw.githubusercontent.com/PactifyLauncherExamples/maven-repository/master/</url>
  </repository>
</repositories>
<dependencies>
  <dependency>
    <groupId>pactify.client.api</groupId>
    <artifactId>plsp-protocol</artifactId>
    <version>20230914</version>
    <scope>compile</scope>
  </dependency>
</dependencies>
```

- `PLSPPacketConfFlag` - Permet d'activer ou de désactiver certains comportements.
  - `"attack_cooldown"` (défaut: on) - Activer/Désactiver le cooldown des attaques
  - `"player_push"` (défaut: on) - Activer/Désactiver le fait que les joueurs se poussent entre eux
  - `"large_hitbox"` (défaut: off) - Activer/Désactiver les hitboxs larges (comme en 1.8)
  - `"sword_blocking"` (défaut: off) - Activer/Désactiver la parade avec l'épée (pour que ça fonctionne sur un serveur 1.9 il faudra modifier son code)
  - `"hit_and_block"` (défaut: off) - Activer/Désactiver le hit-and-block
  - `"old_enchantments"` (défaut: off) - Activer/Désactiver l'ancienne interface d'enchantement (comme en 1.8)
  - `"pvp_hit_priority"` (défaut: off) - Activer/Désactiver le fait de donner la priorité au PVP lors des coups (ex: une entité sera ciblée en priorité plutôt qu'une herbe haute)
  - `"see_chunks"` (défaut: off) - Activer/Désactiver la vision des chunks (le chunk actuel est plus brillant)
  - `"sidebar_scores"` (défaut: on) - Activer/Désactiver les scores dans la sidebar du scoreboard (les nombres en rouge)
  - `"smooth_experience_bar"` (défaut: off) - Activer/Désactiver l'animation lissée de la barre d'expérience
  - `"sort_tab_list_by_names"` (défaut: off) - Activer/Désactiver le trie de la tab-list en fonction du display-name des joueurs
- `PLSPPacketConfFlags` - Permet d'envoyer plusieurs PLSPPacketConfFlag d'un coup.
- `PLSPPacketPlayerModel` - Permet de changer le modèle d'un joueur (pour l'afficher comme étant un zombie par exemple).
- `PLSPPacketEntityMeta` - Permet de changer certaines metadata des entités (scale, tags, opacity).
- `PLSPPacketVignette` - Permet d'activer la vigentte (les contours colorés sur l'écran), avec des couleurs personnalisées.
- `PLSPPacketReset` - Annule toutes les modifications faites par les autres packets, l'état est réinitialisé comme au moment de la connexion au serveur.
