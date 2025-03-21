# Save/Load

js-dos supports saving and restoring game progress. You can play game from time to time
without losing progress. It's working automatically while you don't change bundle url.

This feature works by dumping changes in file system into second `bundle` and use it to override original file system on next load. You can read more about actual implementation [here](Save-Load.md).

Save/Load feature works automatically whenever player press **save icon**. However, game itself should
support storing progress. Read detailed [instructions](Cloud-storage.md#save-and-load-in-dos-games) how to deal with save/load in DOS.

![save-button.jpg](save-button.jpg)

> By default, js-dos store game progress in indexed db of browser. This data can be wiped at any moment by browser.
> You can avoid this problem using js-dos [cloud saves](Cloud-storage.md).
>
{style="warning"}
