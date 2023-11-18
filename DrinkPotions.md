```js
function DrinkPotions()
{
    var normalJewelry = ['0x4008732A', '0x40094716', '0x40019390', '0x40093BCB'];   // jewelry ids without enhance potions
    var enhencedJewelry = ['0x400921CC', '0x40037DFF', '0x40086F75', '0x4009511D']; // jewelry ids with enhance potions
    
    UnEquip();
    Equip(enhencedJewelry);
    
    Orion.Say('[drink strength');
    Orion.Say('[drink agility');    
        
    Orion.Wait(1000);
    
    UnEquip();
    Equip(normalJewelry);
}

function UnEquip()
{
    var types = ['Ring', 'Necklace', 'Bracelet', 'Earrings'];
    
    for(var i = 0; i < types.length; i++){
        Orion.Unequip(types[i]);
        Orion.Wait(600);
    }
}

function Equip(array)
{
    for(var i = 0; i < array.length; i++){
        Orion.Equip(array[i]);
        Orion.Wait(600);
    }
}
```
