```js
function Peacemaking()
{
    var musicInstrumentType = '0x0E9D'; // тип инструмента
    
    while(!Player.Dead() && Orion.SkillValue('Peacemaking', 'real') < Orion.SkillValue('Peacemaking', 'cap'))
    {
        Orion.UseSkill('Peacemaking');
        
        var result = Orion.WaitJournal('shall you play|wish to calm', Orion.Now(), Orion.Now() + 2000);
        if(result && result.FindTextID() == 0)
        {
            Orion.Wait(600);
            
            var instruments = Orion.FindTypeEx(musicInstrumentType, any, 'backpack');
            
            if(instruments.length > 0)
                Orion.TargetObject(instruments[0].Serial());
            else {
                Orion.Print('No music instruments!');
                return;
            }
        }
        else{
            Orion.TargetObject(self);
            Orion.Wait('11s');
        }
    }
}
```
