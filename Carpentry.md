```js
var settings = {
	'maxWeight'               : 410,                // максимальный вес
	'toolType'                   : '0x10E7',         // инструмент, установлен scorp
	'toolsContainerId'        : '0x00000000',  // Id контейнера с инструментом
	'logsContainerId'         : '0x00000000',  // Id контейнера с логами
	'trashBarrelId'             : '0x00000000',  // Id бочки для мусора
	'toolsCountInBackpack' : 5,                   // количество инструментов в бекпаке
	'logsCountInBackpack'  : 380,               //  количество логов которое будет взято из сундука в бекпак (зависит максимального веса персонажа)
	'logs' : { 'type': '0x1BDD', 'color': '0x0000' }
};

var products = {
	'smallCrate'         : { 'type': '0x09A9', 'logsCount' : 8 },
	'mediumCrate'     : { 'type': '0x0E3F', 'logsCount' : 15 },
	'largeCrate'         : { 'type': '0x0E3D', 'logsCount' : 18 },
	'shield'                : { 'type': '0x1B7A', 'logsCount' : 9 },
	'quarterStaff'       : { 'type': '0x0E89', 'logsCount' : 6 },
	'shepherdsCrook' : { 'type': '0x0E81', 'logsCount' : 7 }
};

var SCRIPT_MESSAGES = [
		'OK, DONE !', 
		'NO LOGS !',
		'NO TOOLS!',
	];

function CarpentryMain()
{
	Orion.Print('CarpentryMain has been started !');
	
	while(!Player.Dead() && Orion.SkillValue('Carpentry', 'real') < 1000)
	{
		var result = 0;

		if(Orion.SkillValue('Carpentry', 'real') >= 100 && Orion.SkillValue('Carpentry', 'real') < 310 && result == 0) 
			result = CarpentryService(310, products.smallCrate, 15, 9);
			
		if(Orion.SkillValue('Carpentry', 'real') >= 310 && Orion.SkillValue('Carpentry', 'real') < 473 && result == 0) 
			result = CarpentryService(473, products.mediumCrate, 15, 16);
			
		if(Orion.SkillValue('Carpentry', 'real') >= 473 && Orion.SkillValue('Carpentry', 'real') < 526 && result == 0) 
			result = CarpentryService(526, products.largeCrate, 15, 23);
			
		if(Orion.SkillValue('Carpentry', 'real') >= 526 && Orion.SkillValue('Carpentry', 'real') < 736 && result == 0) 
			result = CarpentryService(736, products.shield, 22, 23);
			
		if(Orion.SkillValue('Carpentry', 'real') >= 736 && Orion.SkillValue('Carpentry', 'real') < 789 && result == 0) 
			result = CarpentryService(789, products.quarterStaff, 22, 9);
			
		if(Orion.SkillValue('Carpentry', 'real') >= 789 && Orion.SkillValue('Carpentry', 'real') < 1000 && result == 0) 
			result = CarpentryService(1000, products.shepherdsCrook, 22, 2);
		
		var textColor = '0x0056';
		if(result != 0)
			textColor = '0x0494';

		Orion.Print(textColor, '--------------------');
		Orion.Print(textColor, SCRIPT_MESSAGES[result]);
		Orion.Print(textColor, '--------------------|');
		
		return;
	}
}

function CarpentryService(maxSkill, product, gumpCategoryButton, gumpProductButton)
{
	while( Orion.SkillValue('Carpentry', 'real') < maxSkill)
	{
		Orion.Print('Making product...!');
		
		var result = ValidateState(product);
		if(result != 0)
			return result;
		
		Orion.UseType(settings.toolType);
		
		if (Orion.WaitForGump(5000))
		{
			Orion.Wait(100);
			var gump0 = Orion.GetGump('last');
			
			if (gump0 !== null && !gump0.Replayed()  && (gump0.ID() === '0x38920ABD')) {
				gump0.Select(Orion.CreateGumpHook(gumpCategoryButton));
				Orion.Wait(200);
			}
		}
		
		if (Orion.WaitForGump(5000))
		{
			var gump1 = Orion.GetGump('last');
			
			if (gump1 !== null && !gump1.Replayed()  && (gump1.ID() === '0x38920ABD')) {
				gump1.Select(Orion.CreateGumpHook(gumpProductButton));
				Orion.Wait(200);
			}
		}
		
		if (Orion.WaitForGump(5000))
		{
			var gump2 = Orion.GetGump('last');
			if ((gump2 !== null) && (!gump2.Replayed()) && (gump2.ID() === '0x38920ABD'))
			{
				gump2.Select(Orion.CreateGumpHook(0));
				Orion.Wait(100);
			}
		}
	}
	
	MoveToTrashBarrel(product.type)

	return 0;
}

function ValidateState(product)
{
	var state = 0;
	
	if(Player.Weight() >= settings.maxWeight)
		MoveToTrashBarrel(product.type);
	
	if((state = GetLogs(product)) != 0)
		return state;

	return GetTools();
}

function GetLogs(product)
{
	Orion.Print('Getting logs...');
	
	var logs = Orion.FindType(settings.logs.type, settings.logs.color, 'backpack');
	var logsCount = 0;
	
	if(logs.length > 0)
		logsCount = Orion.FindObject(logs[0]).Count();

	if(logsCount < product.logsCount)
	{
		MoveToTrashBarrel(product.type);
		Orion.OpenContainer(settings.logsContainerId);
		
		if(Orion.FindType(settings.logs.type, settings.logs.color, settings.logsContainerId).length > 0)
		{
			Orion.Wait(600);
			Orion.MoveItemType(settings.logs.type, settings.logs.color,  settings.logsContainerId, settings.logsCountInBackpack - logsCount);
			Orion.Wait(600);
			
			return 0;
		}
		else
			return 1;
	}
	
	return 0;
}

function GetTools()
{
	Orion.Print('Getting tools...');
	
	if(Orion.FindType(settings.toolType, 'any', 'backpack').length == 0)
	{
		Orion.OpenContainer(settings.toolsContainerId);
		
		if(Orion.FindType(settings.toolType, 'any', settings.toolsContainerId).length > 0)
		{
			Orion.Wait(600);
			
			for(var i = 0; i < settings.toolsCountInBackpack; i++)	{
				Orion.MoveItemType(settings.toolType, 'any',  settings.toolsContainerId, 1);
				Orion.Wait(600);
			}
			
			return 0;
		}
		else
			return 2;
	}
	
	return 0;
}

function MoveToTrashBarrel(productType)
{
	Orion.Print('Moving product to trash barrel...');
	
	while(Orion.FindType(productType, 'any', 'backpack').length > 0)	{
		Orion.MoveItemType(productType, 'any', 'backpack', 1, settings.trashBarrelId);
		Orion.Wait(600);
	}
}
```
