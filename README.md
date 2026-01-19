
# The Unreal Engine Knowledge Hub: Build Better, Build Faster

Documentation is usually a mess of technical jargon and outdated forum posts. We built the **Knowledge Hub** to fix that. This isn't just a collection of notes; it's a strategic framework for mastering Unreal Engine 5.

We've analyzed thousands of production patterns to distill the "Seven Pillars" of engine mastery. Whether you're a solo dev or a technical architect, these resources are designed to help you solve real-world problems.

> You can access the old Markdown document in this repository, or visit my blog for the new one.

<a href="https://www.buymeacoffee.com/creos" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>
---

## The Seven Pillars of Unreal Mastery

### 1. [Architecture & Core Fundamentals](https://artistscompany.net/blog/ue-core-fundamentals)
*Stop fighting the engine.* Learn the class hierarchy, the reflection system, and the macros that make C++ feel as flexible as Blueprints.

### 2. [Advanced Design Patterns](https://artistscompany.net/blog/ue-advanced-cpp-patterns)
*Decouple your logic.* Master Interfaces, Delegates, and high-performance Event Managers to build systems that don't break.

### 3. [Networking: EOS & Steam](https://artistscompany.net/blog/ue-multiplayer-networking-guide)
*Scale globally.* A deep dive into Epic Online Services, session management, and the "Lag-Proof" replication strategies used in modern MOBAs and Shooters.

### 4. [Optimization: The Performance Bible](https://artistscompany.net/blog/ue-performance-optimization)
*Maintain your framerate.* How to profile bottlenecks, manage texture streaming pools, and implement PSO Caching for hitch-free gameplay.

### 5. [Gameplay Systems Cookbook](https://artistscompany.net/blog/ue-gameplay-systems-mechanics)
*The build kit.* Battle-tested C++ scripts for weapon systems, modular spawner architectures, and physics-based interactions.

### 6. [Data & External Integration](https://artistscompany.net/blog/ue-data-and-integrated-systems)
*Manage complexity.* Connecting your game to the web via HTTP/JSON, leveraging the Asset Manager, and getting started with GAS (Gameplay Ability System).

### 7. [Scalable UI & Blueprint Patterns](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns)
*Polished UX.* Best practices for settings menus, persistence systems, and scalable visual scripting for professional user interfaces.

### 8. [Ship Your Game: The Research-First Guide](https://artistscompany.net/blog/shipping-your-first-ue5-game)
*Stop the tutorial loop.* A guide to finding your "Ten-Second Fun Test" and shipping your project.

---

## Full Topic Index (Legacy Notes)

<details>
<summary>Open the original Unreal Engine Scripts &amp; Notes topic list</summary>

0) [Unreal Engine Basics](https://artistscompany.net/blog/ue-core-fundamentals#unreal-engine-basics)
   1) [The Object Class](https://artistscompany.net/blog/ue-core-fundamentals#the-object-class)
   2) [The Actor Class](https://artistscompany.net/blog/ue-core-fundamentals#the-actor-class)
   3) [The Pawn Class](https://artistscompany.net/blog/ue-core-fundamentals#the-pawn-class)
   4) [The Character Class](https://artistscompany.net/blog/ue-core-fundamentals#the-character-class)
   5) [Components](https://artistscompany.net/blog/ue-core-fundamentals#components)
   6) [Custom Material](https://artistscompany.net/blog/ue-core-fundamentals#custom-material)
   7) [Material Inputs](https://artistscompany.net/blog/ue-core-fundamentals#material-inputs)
   8) [Lights](https://artistscompany.net/blog/ue-core-fundamentals#lights)
   9) [Lights Properties](https://artistscompany.net/blog/ue-core-fundamentals#lights-properties)
   10) [Unreal Header Tool (UHT)](https://artistscompany.net/blog/ue-core-fundamentals#unreal-header-tool-uht)
   11) [UPROPERTY](https://artistscompany.net/blog/ue-core-fundamentals#uproperty)
   12) [UFUNCTION](https://artistscompany.net/blog/ue-core-fundamentals#ufunction)
1) [Save and Load Game C++](https://artistscompany.net/blog/ue-data-and-integrated-systems#save-and-load-game-c)
2) [Using DataTable C++](https://artistscompany.net/blog/ue-data-and-integrated-systems#using-datatable-c)
3) [API/Json Usage](https://artistscompany.net/blog/ue-data-and-integrated-systems#apijson-usage)
4) [Optimizing](https://artistscompany.net/blog/ue-performance-optimization#optimizing)
5) [Environment](https://artistscompany.net/blog/ue-performance-optimization#environment)
6) [Garbage Collection on Unreal Engine](https://artistscompany.net/blog/ue-core-fundamentals#garbage-collection-on-unreal-engine)
7) [Multiplayer Replication - LAG - Package Loss](https://artistscompany.net/blog/ue-multiplayer-networking-guide#multiplayer-replication---lag---package-loss)
8) [Multiplayer Server Type Selection](https://artistscompany.net/blog/ue-multiplayer-networking-guide#multiplayer-server-type-selection)
9) [Load Game Hierarchy](https://artistscompany.net/blog/ue-performance-optimization#load-game-hierarchy)
10) [Unreal Engine Diving Levels Course](https://artistscompany.net/blog/ue-performance-optimization#unreal-engine-diving-levels-course)
11) [Export Settings](https://artistscompany.net/blog/ue-performance-optimization#export-settings)
12) [BP Save Graphic and Audio Settings](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-save-graphic-and-audio-settings)
13) [Audio Settings](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#audio-settings)
14) [Graphic Settings (with list)](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#graphic-settings-with-list)
15) [Graphic Settings (with button)](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#graphic-settings-with-button)
16) [Create Game Instance and Save](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#create-game-instance-and-save)
17) [Open Next Level](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#open-next-level)
18) [Draw Debug Line](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#draw-debug-line)
19) [Rotate Turret (2 Methods)](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#rotate-turret-2-methods)
20) [Health Box](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#health-box)
21) [Obstacle](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#obstacle)
22) [Spawner V2](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#spawner-v2)
23) [Gun](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#gun)
24) [Projectile](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#projectile)
25) [Spawner](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#spawner)
26) [Movable Platform](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#movable-platform)
27) [Grab](https://artistscompany.net/blog/ue-gameplay-systems-mechanics#grab)
28) [BP Create Collectable Object](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-create-collectable-object)
29) [BP Set Game Options](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-set-game-options)
30) [BP Load Options](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-load-options)
31) [BP Load Save](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-load-save)
32) [BP Create Game Options](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-create-game-options)
33) [BP Create Save](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#bp-create-save)
34) [Asset Manager](https://artistscompany.net/blog/ue-data-and-integrated-systems#asset-manager)
35) [Gameplay Ability Plugin](https://artistscompany.net/blog/ue-data-and-integrated-systems#gameplay-ability-plugin)
36) [Server Type Check](https://artistscompany.net/blog/ue-multiplayer-networking-guide#server-type-check)
37) [Game Start with CMD](https://artistscompany.net/blog/ue-multiplayer-networking-guide#game-start-with-cmd)
38) [Steam Lobby System C++ (GameDevTV Course)](https://artistscompany.net/blog/ue-multiplayer-networking-guide#steam-lobby-system-c-gamedevtv-course)
39) [Puzzle Platform](https://artistscompany.net/blog/ue-multiplayer-networking-guide#puzzle-platform)
40) [Multiplayer lobby (Multiplayer with hamachi or any IP address)](https://artistscompany.net/blog/ue-multiplayer-networking-guide#multiplayer-lobby-multiplayer-with-hamachi-or-any-ip-address)
41) [Character Control](https://artistscompany.net/blog/ue-core-fundamentals#character-control)
42) [C++ to Blueprint Node](https://artistscompany.net/blog/ue-core-fundamentals#c-to-blueprint-node)
43) [Widget Animation C++](https://artistscompany.net/blog/ue-blueprint-and-ui-patterns#widget-animation-c)
44) [Image Render](https://artistscompany.net/blog/ue-performance-optimization#image-render)
45) [EOS Crossplay Multiplayer C++](https://artistscompany.net/blog/ue-multiplayer-networking-guide#eos-crossplay-multiplayer-c)
46) [OOP on Unreal Engine C++](https://artistscompany.net/blog/ue-advanced-cpp-patterns#oop-on-unreal-engine-c)

</details>

## About This Library

These notes are maintained by **Barış Eroğlu** and represent years of analysis across thousands of production patterns.

**Github:** [creosB](https://github.com/creosB)
**Youtube:** [CreosGaming](https://www.youtube.com/c/CreosGaming)
**Twitch:** [creosb](https://www.twitch.tv/creosb)

*Looking for something specific? Use the links above to jump into a dedicated pillar.*

---

# License

---

## Please contact me first to share or make changes anywhere.
***
This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg
***

Feel free to adjust any specifics to better match your extension’s functionality or your preferred contact details!

<a href="https://artistscompany.net/">
  <img src="https://raw.githubusercontent.com/creosB/presentation/main/background.png" alt="Artists Company" width="800">
</a>