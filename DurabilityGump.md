```js
var layers = [
	[1, 'Одноручное'],	
	[2, 'Двуруч/Щит'], 
	[6, 'Шлем'], 
	[19, 'Локти'], 
	[7, 'Перчатки'], 
	[13, 'Торс'], 
	[4, 'Ноги'],
	[10, 'Шея'], 
	[8, 'Кольцо'],
	[14, 'Браслет'],
	[18, 'Серьги'],	
	[17, 'Лента'], 
	[3, 'Обувь'], 
	[22, 'Роба'], 
	[20, 'Плащь/Колчан'],
	[9, 'Талик'],	
	[12, 'Фартук'],
];

function DurabilityGump()
{
	Shared.AddVar('StopDurabilityGumpScript', false);
	
	while(true)
	{
		if(Player.Dead()){
			Orion.Wait(500);
			continue;
		}
		
		if(Shared.GetVar('StopDurabilityGumpScript', false))
			return;
			
		Refresh(true);
		Orion.Wait('30s');
	}
}

function ConigureGump(functionCallStub)
{
	gump = Orion.CreateCustomGump(101);   
    gump.SetNoClose(false);
	gump.Clear();    
 	gump.SetCallback('Refresh');
 	
	return gump;
}

function Refresh(firststart)
{   
    var extendedModeEnabled = Shared.GetVar('ExtendedMode', false);
	var gump = ConigureGump();
	var returnCode = CustomGumpResponse.ReturnCode();
	
	if(returnCode == 0 && !firststart) {
		gump.Close();
		Shared.AddVar('StopDurabilityGumpScript', true);
		return;
	}

	if(returnCode == 1) 
	{
		if(extendedModeEnabled) {
			extendedModeEnabled = false;
			Shared.AddVar('ExtendedMode', false);
		}
		else {
			extendedModeEnabled = true;
			Shared.AddVar('ExtendedMode', true);
		}
	}
	
	var width = 230;
	var x = 20;
    var y = 45;
    
    var data = CreateData();
	
	if(data.durabilityData.length > 0)
	{
		gump.AddGumpPic(10, 15, '2624', '0', '0', '0', width - 20, data.durabilityData[data.durabilityData.length - 1].y + 40); // bg
					
		for (var i = 0; i < data.durabilityData.length - 1; i++) 
		{
			gump.AddText(x, data.durabilityData[i].y, 0xFFFE,  data.durabilityData[i].layer);
			
			if(data.durabilityData[i].percents != '----'){
				gump.AddText(x+110, data.durabilityData[i].y, 
					(data.durabilityData[i].percents  > 75 ? 0x0106: 
						(data.durabilityData[i].percents  < 25 ? 0x0026: 0x002B)), '[' + data.durabilityData[i].percents +'%]');
			}
			
			if(data.durabilityData[i].insured == 'B')
				gump.AddText(x+165, data.durabilityData[i].y, 0x0106, 'B');
			else
				gump.AddText(x+165, data.durabilityData[i].y + 2, (data.durabilityData[i].insured ? 0x0106 : 0x0026), (data.durabilityData[i].insured ? '•' : '!!!'));
	
			y = data.durabilityData[i].y;
		}
		
		
		y = data.durabilityData[data.durabilityData.length - 1].y;
	}
	gump.AddLine(120, y + 22, width - 20, y + 22, '#AA7B7B7B', 1);
	gump.AddText(30, y + 10, '0x070A', 'Extended mode');
	
	y += 15;

	if(extendedModeEnabled)
	{
		gump.AddGumpPic(10, y + 10, '2624', '0', '0', '0', width - 20, 180); // bg
		gump.AddButton(1, 10, y - 3, '5602', '5602',  '5606', '#010101');

		for(var j =0; j < data.additionalParams.length; j++)
        {
        	if(j % 2 == 0) {
        		cx = 23;
        		y += 18
     		}
     		else 
     			cx = 127;
     			
			gump.AddText(cx, y, 0xFFFE, data.additionalParams[j].ABBR +':' );
			gump.AddText(cx + 55, y, 0x0106, data.additionalParams[j].val);
		}

		gump.AddLine(5, y + 30, width - 20, y + 30, '#AA7B7B7B', 1);
		y += 60;
	}
	else 
	{
   		gump.AddButton(1, 10, y - 3, '5601', '5601',  '5605', '#010101');
   		y += 40
   	}

    gump.AddText(22, 10, '0x070A',  '<basefont size=17>Layer</basefont>');
	gump.AddText(132, 10, '0x070A', '<basefont size=17>%</basefont>');
	gump.AddText(168, 10, '0x070A', '<basefont size=17>Insur.</basefont>');
	
	gump.AddLine(0, 35, width - 20, 35, '#AA7B7B7B', 1);
	
    gump.AddButton(2, 13, y - 20, '4005', '4006',  '4007', '#010101');
	gump.AddText(55, y - 20, '0x070A', 'Refresh');
	
	gump.AddGumpPic(0, 0, '2621', '0', '0', '0', width, 15); // top
	
	gump.AddGumpPic(0, 0, '2623', '0', '0', '0', 10, y); // left
	gump.AddGumpPic(width - 20, 0, '2625', '0', '0', '0', 20, y); // right
	
	gump.AddGumpPic(0, 0, '2620', '0', '0', '0', 15, 15); // top left corner
	gump.AddGumpPic(width - 20, 0, '2622', '0', '0', '0', 20, 15); // top right corner

	gump.AddGumpPic(0, y, '2627', '0', '0', '0', width, 18); // bottom
	
	gump.AddGumpPic(0, y, '2626', '0', '0', '0', 15, 18); // bottom left corner
	gump.AddGumpPic(width - 20, y, '2628', '0', '0', '0', 20, 18); // bottom right corner

	//gump.SetX(1720);
	//gump.SetY(1);
	gump.Update();
	
}


function CreateData()
{	
	var additionalParams = [
			{'name' : 'Faster Casting', 'val': 0, 'ABBR' : 'FC'},
			{'name' : 'Faster Cast Recovery', 'val': 0, 'ABBR' : 'FCR'},
	    	{'name' : 'Spell Damage Increase', 'val': 0, 'ABBR' : 'SDI'},
	    	{'name' : 'Lower Reagent Cost', 'val': 0, 'ABBR' : 'LRC'},
	    	{'name' : 'Lower Mana Cost', 'val': 0, 'ABBR' : 'LMC'},
	    	{'name' : 'Damage Increase', 'val': 0, 'ABBR' : 'DI'},
	    	{'name' : 'Hit Chance Increase', 'val': 0, 'ABBR' : 'HCI'},
	    	{'name' : 'Defense Chance Increase', 'val': 0, 'ABBR' : 'DCI'},
	    	{'name' : 'Strength Bonus', 'val': 0, 'ABBR' : '+Str'},
	    	{'name' : 'Hit Point Increase', 'val': 0, 'ABBR' : '+HP'},
	    	{'name' : 'Dexterity Bonus', 'val': 0, 'ABBR' : '+Dex'},
	    	{'name' : 'Stamina Increase', 'val': 0, 'ABBR' : '+Stam'},
	    	{'name' : 'Intelligence Bonus', 'val': 0, 'ABBR' : '+Int'},
	    	{'name' : 'Mana Increase', 'val': 0, 'ABBR' : '+Mana'},
	    ];

	var durabilityData = [];
	var y = 45;
	
	for (var i = 0; i < layers.length; i++) 
	{
		var item = Orion.ObjAtLayer(layers[i][0]);
		
		if(item != null)
		{
			var fullProps = item.Properties();
			var props =  fullProps.split("SET ITEM");
			
     		var regEx = /(Durability\D*)(\d*\s\d*\s\/\s\d*)/g;
     		var matches = regEx.exec(props[0]);
     		var insured = fullProps.indexOf('Insured') != -1;
     		var blessed = fullProps.indexOf('Blessed') != -1 ? 'B' : null;
     		
     		if(matches  != null)
     		{
     			var durCurMax = matches[2].split('/');
      			var percents =  Math.round(durCurMax[0] * 100 / durCurMax[1]);

				durabilityData.push({'layer': layers[i][1], 'percents': percents, 'durMax': durCurMax, 'y': y, 'insured': (blessed == 'B' ? blessed : insured)});
				
				y += 15;
         	}
         	else{
         		durabilityData.push({'layer': layers[i][1], 'percents': '----', 'durMax': 0, 'y': y, 'insured':  (blessed == 'B' ? blessed : insured)});
         		y += 15;
         	}

      		for(var j =0; j < additionalParams.length; j++)
         	{
	         	regEx = new RegExp("(" + additionalParams[j].name + "[a-z ]*)(-?\\d*)");
	     		matches = regEx.exec(props[0]);
	     		
				if(matches  != null)
     				additionalParams[j].val +=  parseInt(matches[2]);
	     	}
		}     
	}
	
	if(durabilityData.length > 0)
		durabilityData.push({'y': durabilityData[durabilityData.length - 1].y + 20});
	
	var finalData = 
	{
		'durabilityData': durabilityData,
		'additionalParams': additionalParams
	};
	
	return finalData;
}
```
![image](https://github.com/user-attachments/assets/552702de-4d04-4a98-848c-b6226d116923)

![image](https://github.com/user-attachments/assets/53f25840-418a-4b58-99eb-8bbdc544b5db)

