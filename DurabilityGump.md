```js
function DurabilityGump()
{
    var gump = Orion.CreateCustomGump(101);   
    gump.SetNoClose(false);
    
    var layers = [
            [1, 'Одноручное'],
            [2, 'Двуручное/Щит'],
            [6, 'Шлем'],
            [7, 'Перчатки'],
            [8, 'Кольцо'],
            [9, 'Талисман'],
            [10, 'Шея'],
            [13, 'Торс'],
            [14, 'Браслет'],
            [18, 'Серьги'],
            [19, 'Локти'],
         ];
            
    while(!Player.Dead())
    {
        gump.Clear();    
    
        var x = 0;
        var y = 0;
        
        for (var i = 0; i < layers.length; i++) 
        {
            var item = Orion.ObjAtLayer(layers[i][0]);
    
            if(item !=null)
            {
                 var props =  item.Properties();
                 var regEx = /(Durability\D*)(\d*.*\/\d*.*)/g;
                 var matches = regEx.exec(props);
                 
                 if(matches  != null)
                 {
                    var durCurMax = matches[2].split('/');
                    var percents =  Math.round(durCurMax[0]* 100 / durCurMax[1]);
    
                    gump.AddText(x, y, (percents  > 75 ? 0x0106: (percents  < 25 ? 0x0026: 0x002B)), '[' + percents +'%]');
                    gump.AddText(x+45, y, 0xFFFE,  layers[i][1]);
                    y += 15;
                 }
            }     
        }

        gump.Update();
        Orion.Wait('5m');
    }
}
```
