# Cloud saves

By default, js-dos stores your game progress in the browser's indexed database. 
This is a great no-cost solution, but it has some **limitations**:

* The browser can clear the database at any moment.
* It's not possible to play the same game using different devices.

To address these problems, js-dos offers a cloud saves service. 
For [subscribed](Subscription.md) users, js-dos automatically stores the saves in the cloud, allowing you 
to have your progress synced across all your devices. With this feature, you can now enjoy seamless 
gameplay experiences across different platforms.

## Save and Load in DOS Games

js-dos offers you a great and free service to play DOS games. Many DOS games are fantastic and can provide hours of gameplay. Obviously, it's not possible to finish these games in one session. Luckily, js-dos fully supports saving and restoring game progress.

First things first: DOS games were developed at the beginning of the gaming industry, and typically they do not have auto-saving or continuous saving while you play. You should explicitly save your progress within the game.

To save your progress, you need to follow the game's original saving mechanism, as js-dos emulates the DOS environment, allowing games to run as they did on original hardware. Here's a general approach you might find helpful:

1. **Access the Game's Save Menu**: Most DOS games have an in-game save option. This is usually accessed through the game's main menu, which can often be brought up by pressing the "Esc" key or a specific function key (e.g., F1, F2, etc.). From there, navigate to the save game option using the arrow keys and select it by pressing "Enter".

2. **Use In-game Commands**: Some games, especially text-based or adventure games, allow you to save by typing in a save command. Common commands include "save", "save game", or something similar. After typing the command, you might need to specify a file name for your save game.

3. **Check the Game's Manual**: Since the saving process can vary significantly from one game to another, it's a good idea to check the game's manual or any available help files. These documents often include specific instructions on how to save your game. Manuals can sometimes be found online or within the game's folder on dos.zone.

4. **Keyboard Shortcuts**: Some games implement quick save/load features through keyboard shortcuts. Common shortcuts include pressing keys like F5 to save and F9 to load (though these can vary). Try to look for these shortcuts in the game's manual or settings menu.

5. **Emulator Options**: dosbox-x also offers specific options for saving your game state. This feature saves the exact state of the emulator, allowing you to resume from the exact moment you saved, regardless of the in-game save system. Look for this option in js-dos sidebar. (dosbox-x only!)

Your game progress has been successfully saved! To load it, simply follow the same load mechanism provided by the game.

## Keep saves between page reload

After saving the game using the game's built-in mechanism, you should explicitly inform the JS-DOS player that the game saves have been updated and that you want to store them persistently (in the cloud, IndexedDB, etc.). To do this, you must click the "diskette" button in the JS-DOS sidebar. If you do not do this, your progress will be immediately lost upon restarting the page.

![save-button.jpg](save-button.jpg)

When your saves are successfully stored in persistent storage, you will receive a green toast notification. Now, you can reload the page, and your saves will be retained!

## Keep saves between sessions/browsers/devices

For non-subscribed users, js-dos provides a free service that stores your saves in the browser's IndexedDB. However, this has limitations: it only works within the same browser, and the saves can be wiped suddenly by the browser.

For **subscribed users**, saves are automatically stored in cloud storage and will be available on all your devices.
