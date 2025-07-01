# A Link to the Past Barcode Battler II Web App

## Overview
This project is a web-based adaptation of The Legend of Zelda: A Link to the Past (Barcode Battler II). Players can engage in a unique card-based dungeon exploration game where barcodes generate warriors, items, and enemies. Navigate through challenges, defeat enemies, and level up to ultimately face Ganon.

## Features
- **Barcode Scanning**: Generate warriors, items, and enemies by scanning or inputting barcodes. Note: An actual barcode scanner is not required.
- **Dungeon Exploration**: Arrange cards into a triangle and navigate through the dungeon by selecting cards.
- **Turn-Based Combat**: Engage in battles using dice rolls and equipped items to defeat enemies.
- **Level-Up System**: Collect Heart Container pieces to restore health and strengthen attacks.
- **Item Management**: Equip items to boost dice rolls or gain special abilities.
- **Replay Value**: Randomized card placement ensures unique challenges every time.
- **Localization**: Support for multiple languages to enhance accessibility.
- **Game Modes**: Play using either C2 mode or D6 mode.

## Game Mechanics
### Preparation
- Select the Link (not Magic Link) and Heart cards from the deck and add them to your hand.
- Shuffle the remaining cards and arrange them into a triangle.
- Have a six-sided die (D6) ready for D6 mode or follow the rules for C2 mode.
- Place the Heart card color side up to indicate Link has 2 hearts.

### Dungeon Navigation
- Choose a card from the outer edges of the triangle and place the Link card on it.
- If the card is an Item, add it to your hand. If it is an Enemy, prepare for battle.
- After collecting an Item or winning a battle, move Link to an adjacent card.
- The goal is to reach and defeat Ganon.

### Combat System
- Match or exceed the number on the Enemy card with a roll of the D6 to defeat it (D6 mode).
- Equip items to boost dice rolls or gain additional rolls.
- Some enemies are immune to magic, restricting certain items.
- Defeated enemies are added to your hand.

### Level-Up System
- Defeated enemies provide Heart Container pieces.
- Collect enough pieces to form a full Heart Container to level up.
- Leveling up restores health and strengthens attacks.

### Special Items
- Pegasus Shoes and Magic Cloak allow Link to jump over empty spaces.
- Items have limited uses; consult the Cards page for details.

### Defeat
- If Link loses a dice roll, he is defeated and loses a heart.
- If Link starts a battle with only 1 heart and loses, the game is over.

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/CaptainChrispy/zelda-barcode-battler.git
   ```
2. Navigate to the project directory:
   ```bash
   cd zelda-barcode-battler
   ```
3. Install dependencies:
   ```bash
   npm install
   ```
4. Start the development server:
   ```bash
   npm run serve
   ```

## Technologies Used
- **Frontend Framework**: Vue.js
- **Animation Library**: GSAP (GreenSock Animation Platform)
- **Backend**: TBD
- **Database**: TBD
- **Localization**: Vue I18n or similar library for multi-language support