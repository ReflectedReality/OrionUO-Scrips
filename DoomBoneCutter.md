function DoomBoneCutter()
{
	while(true)
	{
		if(Player.Dead()) {
			Orion.Wait(1000);
			continue;
		}
		
		var found = Orion.FindTypeEx('0x0ECA|0x0ECB|0x0ECC|0x0ECD|0x0ECE|0x0ECF|0x0ED0|0x0ED1|0x0ED2', any, ground);
		
		if(found.length > 0)
		{   
			if (found[0].Exists())
			{
				Orion.UseObject('0x4003C15F');
				Orion.WaitForTarget(3000);
				Orion.TargetObject(found[0].Serial());
			}
		}
		
		Orion.Wait(500);
	}
}
