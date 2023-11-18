```js
function AutoLooter()
{
    var lists = 'Gems|GP|Leater' // Orion find lists

    while (true)
     {
         while (Player.Dead()) Orion.Wait(1000);
         
         var corpses = Orion.FindType(0x2006, -1, ground, '', 2)
          
         if (corpses.length > 0)
          {
              for (var c = 0; c < corpses.length; c++)
               {    
                    var _item = Orion.FindList(lists, corpses[c], '', 2, 4, true);     

                    for (i=0;i<_item.length;i++)
                    {
                        Orion.MoveItem (_item[i],0);
                        Orion.Wait(600);
                    }
               }
          }
          
         Orion.Wait(200);
     }
}
```
