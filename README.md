# TradeStation_Backtester
Developed and deployed a fully automated trading strategy using TradeStation’s EasyLanguage, a Pascal-like scripting language optimized for systematic trading. The strategy was implemented on a TopStep simulated futures account and successfully achieved over 100% return, scaling the account from $4,500 to over $9,000 within a four-month period.

The code below is written in EasyLanguage a proprietary language similar to Pascal in syntax
---------------------------------------------------------------------
Variables: 

	{Today's Date Info}
	string dateString(""),
	string formattedDate(""),
	bool itsaWeekday(true),
	bool itsaHoliday(false),
	bool flag1(false),					//flag for reporting day of week
	bool flag2(false),					//flag for reporting a holiday
	
	{Holidays}
	string newyearsday20("01/01/20"),
	string martinlutherday20("01/20/20"),
	string presidentsday20("02/17/20"),
	string goodfriday20("04/10/20"),
	string memorialday20("05/25/20"),
	string fourthofjuly20("07/03/20"),
	string laborday20("09/07/20"),
	string thanksgiving20("11/26/20"),
	string blackfriday20("11/27/20"),
	string christmaseve20("12/24/20"),
	string christmas20("12/25/20"),
	
	string newyearsday21("01/01/21"),
	string martinlutherday21("01/18/21"),
	string presidentsday21("02/15/21"),
	string goodfriday21("04/02/21"),
	string memorialday21("05/31/21"),
	string fourthofjuly21("07/05/21"),
	string laborday21("09/06/21"),
	string thanksgiving21("11/25/21"),
	string blackfriday21("11/26/21"),
	string christmaseve21("12/23/21"),
	string christmas21("12/24/21"),
	
	string newyearsday22("01/01/22"),
	string martinlutherday22("01/17/22"),
	string presidentsday22("02/21/22"),
	string goodfriday22("04/15/22"),
	string memorialday22("05/30/22"),
	string juneteenth22("06/20/22"),
	string fourthofjuly22("07/04/22"),
	string laborday22("09/05/22"),
	string thanksgiving22("11/24/22"),
	string blackfriday22("11/25/22"),
	string christmas22("12/26/22"),
	
	string newyearsday23("01/02/23"),
	string martinlutherday23("01/16/23"),
	string presidentsday23("02/20/23"),
	string goodfriday23("04/07/23"),
	string memorialday23("05/29/23"),
	string juneteenth23("06/19/23"),
	string fourthofjuly23("07/04/23"),
	string laborday23("09/04/23"),
	string thanksgiving23("11/23/23"),
	string blackfriday23("11/24/23"),
	string christmas23("12/25/23"),
	
	string newyearsday24("01/01/24"),
	string martinlutherday24("01/15/24"),
	string presidentsday24("02/19/24"),
	string goodfriday24("03/29/24"),
	string memorialday24("05/27/24"),
	string juneteenth24("06/19/24"),
	string fourthofjuly24("07/04/24"),
	string laborday24("09/02/24"),
	string thanksgiving24("11/28/24"),
	string blackfriday24("11/29/24"),
	string christmaseve24("12/24/24"),
	string christmas24("12/25/24"),
	
	string newyearsday25("01/01/25"),
	string martinlutherday25("01/20/25"),
	string presidentsday25("02/17/25"),
	string goodfriday25("04/18/25"),
	string memorialday25("05/26/25"),
	string juneteenth25("06/19/25"),
	string fourthofjuly25("07/04/25"),
	string laborday25("09/01/25"),
	string thanksgiving25("11/27/25"),
	string blackfriday25("11/28/25"),
	string christmaseve25("12/24/25"),
	string christmas25("12/25/25"),
	
	{Opening Information}
	double openingPrice(0),
	double dollarPERtick(1.25),							//ES = 12.5, NQ = 5, YM = 5, MES = 1.25
	int ticksperPOINT(4),								//How many ticks are in 1 point? ES = 4 ticks per 1 point, MES = 4 ticks per 1 point, YM = 1 tick per 1 point
	string openingasString(""),
	bool openbell(false),
	
	{First Line General} //***TRADING REVERSAL***
	double FLprofitTarget(0),
	double FLstopLoss(0),
	double FLstopLossLIMIT((minmove/pricescale) * 9999),			//The do not exceed limit of the stop loss
	double dailyLOSScountLIMIT(2),								//The amount of losses to NOT exceed before shutting off the trading system
	double dailyLOSScount(0),							//This just counts the daily losses
	
	double profitMULT(0),									//12.35 (30 ticks) is the base standard value add or subtract 2.06 from this as needed. 2.06 is about 5 ticks
	double   lossMULT(26),									//10.29 (25 ticks) is the base standard value add or subtract 2.06 from this as needed. 2.06 is about 5 ticks
	double volumeMULT(2.00),
	double ATRMULT(1.15),
	double ROClimit(0.08),
	double wickLIMIT(0.60),
	
	double comCOST(1.42),								//Cost of commission ROUND TRIP. Rithmic, MES = $1.42, ES = $4.36, YM = $4.36
	int slipAMOUNT(0),								    //FOR MARKET ENTRIES. The maximum number of ticks slippage could occur on ENTRY. This includes zero and 1 less than the number in the parenthesis. Zero is no slippage. Needed for market order ENTRIES only
	int stop_slipAMOUNT(4),								//FOR STOP MARKET orders. This is your stop loss order
	int limitNOFILL(0),									//Number in parenthesis IS the percent chance of a limit order NOT filling. [DONT SUBRTRACT 1]
	
	double ATR10(0),
	double ATR1(0),
	double ROC(0),
	double SMA25(0),
	double SMA65(0),
	double EMA50(0),	//fake
	double entryfromSIGDIST(0),
	
	bool strongVOL(false),
	bool atrSIZEABLE(false),
	bool ROCinlimit(false),
	bool topWICK(false),
	bool bottomWICK(false),
	bool twflag(false),
	bool bwflag(false),
	
	double slipFTL(0),	 							    
	double slipFTS(0),
	double slipSTL(0),
	double slipSTS(0),
	
	double st_slipFTL(0),	 							    
	double st_slipFTS(0),
	double st_slipSTL(0),
	double st_slipSTS(0),
	
	double FTLnofillNUM(0),
	double FTSnofillNUM(0),
	double STLnofillNUM(0),
	double STSnofillNUM(0),
	
	bool FTLnotfilled(false),
	bool FTSnotfilled(false),
	bool STLnotfilled(false),
	bool STSnotfilled(false),
	
	double firstTRADEentry(0),
	double secondTRADEentry(0),							//Minimum Value LONG = (firstLINEdist + FLproiftTarget + 5). Minimum Value Short = (firstLINEdist - FLproiftTarget - 5) 
	
	double firstTRADEtime(0),
	double secondTRADEtime(0),
	
	double timegap(4),									//This is the time gap in minutes between trade 1 and trade 2
	
	double ATRvalue(14),
	double VolumeMA(0),
	double PLamount(0),
	double MAXamount(0),								//Maximum amount a profit should be on any given trade
	double MINamount(0),								//Minimum amount a loss should be on a y given trade
	bool reportsent(false),
	bool notrade(false),
	bool FTLconfirm(false),
	bool STLconfirm(false),
	bool FTSconfirm(false),
	bool STSconfirm(false),
	bool FTSnotif(false),
	bool FirstTradeLong(false),
	bool FirstTradeShort(false),
	bool SecondTradeLong(false),
	bool SecondTradeShort(false),
	bool FTLentered(false),
	bool FTLexited(false),
	bool FTSentered(false),
	bool FTSexited(false),
	bool STLentered(false),
	bool STLexited(false),
	bool STSentered(false),
	bool STSexited(false);
	
//-------End of Variables-------
	
//-------Today's Date Info-------
dateString = NumtoStr(date, 0); 				    //Today's date as a string
formattedDate = (MidStr(dateString, 4, 2))+"/"+(MidStr(dateString, 6, 2))+"/"+(MidStr(dateString, 2, 2)); //formatted today's date as: MM/DD/YY

If Dayofweek(date) = 0.00 then Begin
	itsaWeekday = false;
End;
If Dayofweek(date) = 1.00 then Begin
	itsaWeekday = true;
End;
If Dayofweek(date) = 6.00 then Begin
	itsaWeekday = false;
End;

If reportsent = true then Begin			//double checks reporting is false before any trades are made
	reportsent = false;
End;

If itsaHoliday = true then Begin		//Makes sure the script doesn't carry over last Holiday's check into today. Acts as a double check
	itsaHoliday = false;
End;

If itsaHoliday = false and formattedDate = newyearsday20 or formattedDate = martinlutherday20 or formattedDate = presidentsday20 or formattedDate = goodfriday20 or formattedDate = memorialday20 or formattedDate = fourthofjuly20 or formattedDate = laborday20 or formattedDate = thanksgiving20 or formattedDate = blackfriday20 or formattedDate = christmaseve20 or formattedDate = christmas20 or formattedDate = newyearsday21 or formattedDate = martinlutherday21 or formattedDate = presidentsday21 or formattedDate = goodfriday21 or formattedDate = memorialday21 or formattedDate = fourthofjuly21 or formattedDate = laborday21 or formattedDate = thanksgiving21 or formattedDate = blackfriday21 or formattedDate = christmaseve21 or formattedDate = christmas21 or formattedDate = newyearsday22 or formattedDate = martinlutherday22 or formattedDate = presidentsday22 or formattedDate = goodfriday22 or formattedDate = memorialday22 or formattedDate = juneteenth22 or formattedDate = fourthofjuly22 or formattedDate = laborday22 or formattedDate = thanksgiving22 or formattedDate = blackfriday22 or formattedDate = christmas22 or formattedDate = newyearsday23 or formattedDate = martinlutherday23 or formattedDate = presidentsday23 or formattedDate = goodfriday23 or formattedDate = memorialday23 or formattedDate = juneteenth23 or formattedDate = fourthofjuly23 or formattedDate = laborday23 or formattedDate = thanksgiving23 or formattedDate = blackfriday23 or formattedDate = christmas23 or formattedDate = newyearsday24 or formattedDate = martinlutherday24 or formattedDate = presidentsday24 or formattedDate = goodfriday24 or formattedDate = memorialday24 or formattedDate = juneteenth24 or formattedDate = fourthofjuly24 or formattedDate = laborday24 or formattedDate = thanksgiving24 or formattedDate = blackfriday24 or formattedDate = christmaseve24 or formattedDate = christmas24 or formattedDate = newyearsday25 or formattedDate = martinlutherday25 or formattedDate =
presidentsday25 or formattedDate = goodfriday25 or formattedDate = memorialday25 or formattedDate = juneteenth25 or formattedDate = fourthofjuly25 or formattedDate = laborday25 or formattedDate = thanksgiving25 or formattedDate = blackfriday25 or formattedDate = christmaseve25 or formattedDate = christmas25 then begin
	itsaHoliday = true;
End;
//-------end-------

//-------Captures opening price-------
If time = 0940 and openbell = false then begin

	openingPrice = open;									//collects opening price
	
	openingasString = NumtoStr(openingPrice, 2);			//converts opening price to a string
	print(formattedDate, ": ", "*******");						//prints todays date with opening price
	
	If itsaHoliday = true and flag2 = false then Begin		//Notifies of a holiday
		print("It's a Holiday!");
		reportsent = true;
		flag2 = true;
		notrade = true;
		openbell = false;
	End
	Else Begin
		openbell = true;
	End;
	
	If itsaWeekday = true and flag1 = false then Begin		//Notifies of a weekday
		flag1 = true;
	End;
		
End;

//-------end-------

//-------Indicator Calculations-------
	VolumeMA = (Ticks[0] + Ticks[1] + Ticks[2] + Ticks[3] + Ticks[4] + Ticks[5] + Ticks[6] + Ticks[7] + Ticks[8] + Ticks[9]) / 10;
	ATR10 = ( (high[0] - low[0]) + (high[1] - low[1]) + (high[2] - low[2]) + (high[3] - low[3]) + (high[4] - low[4]) + (high[5] - low[5]) + (high[6] - low[6]) + (high[7] - low[7]) + (high[8] - low[8]) + (high[9] - low[9]) ) / 10;
	ATR1 = high[0] - low[0];
	ROC = 100 * ((close - close[9]) / close[9]);
	SMA25 = (close[0] + close[1] + close[2] + close[3] + close[4] + close[5] + close[6] + close[7] + close[8] + close[9] + close[10] + close[11] + close[12] + close[13] + close[14] + close[15] + close[16] + close[17] + close[18] + close[19] + close[20] + close[21] + close[22] + close[23] + close[24]) / 25;
	SMA65 = (close[0] + close[1] + close[2] + close[3] + close[4] + close[5] + close[6] + close[7] + close[8] + close[9] + close[10] + close[11] + close[12] + close[13] + close[14] + close[15] + close[16] + close[17] + close[18] + close[19] + close[20] + close[21] + close[22] + close[23] + close[24] + close[25] + close[26] + close[27] + close[28] + close[29] + close[30] + close[31] + close[32] + close[33] + close[34] + close[35] + close[36] + close[37] + close[38] + close[39] + close[40] + close[41] + close[42] + close[43] + close[44] + close[45] + close[46] + close[47] + close[48] + close[49] + close[50] + close[51] + close[52] + close[53] + close[54] + close[55] + close[56] + close[57] + close[58] + close[59] + close[60] + close[61] + close[62] + close[63] + close[64]) / 65;
	EMA50 = (SMA25 + SMA65) / 2;
	
	//Slippage calculation for ENTRIES
	slipFTS = (Floor(Random(slipAMOUNT)) * -1) * (minmove/pricescale);
	slipFTL = (Floor(Random(slipAMOUNT))) * (minmove/pricescale);
	slipSTS = (Floor(Random(slipAMOUNT)) * -1) * (minmove/pricescale);
	slipSTL = (Floor(Random(slipAMOUNT))) * (minmove/pricescale);
	
	//Slippage calculation for STOPLOSSES
	st_slipFTS = (Floor(Random(stop_slipAMOUNT))) * (minmove/pricescale);
	st_slipFTL = (Floor(Random(stop_slipAMOUNT))) * (minmove/pricescale);
	st_slipSTS = (Floor(Random(stop_slipAMOUNT))) * (minmove/pricescale);
	st_slipSTL = (Floor(Random(stop_slipAMOUNT))) * (minmove/pricescale);
	
	//Limit order NO FILL calculation
	FTLnofillNUM = Floor(Random(101));
	FTSnofillNUM = Floor(Random(101));
	STLnofillNUM = Floor(Random(101));
	STSnofillNUM = Floor(Random(101));
	
	//Identifies if the strength of the volume is significant
	If Ticks[0] > (VolumeMA * volumeMULT) then Begin
		strongVOL = true;
	End
	Else Begin
		strongVOL = false;
	End;
	
	//Identifies if the ATR is significant
	If ATR1 > (ATR10 * ATRMULT) then Begin
		atrSIZEABLE = true;
	End
	Else Begin
		atrSIZEABLE = false;
	End;
	
	//Identifies if the ROC is within limit
	If Absvalue(ROC) > ROClimit then Begin
		ROCinlimit = true;
	End
	Else Begin
		ROCinlimit = false;
	End;
	
	//Identifies if the top wick is substantial
	If (close > open) and ((high - close) >= ((close - open) * wickLIMIT)) and ((high - close) >= ((open - low) * (wickLIMIT + 1))) then Begin
		topWICK = true;
		twflag = true;
	End
	Else Begin
		topWICK = false;
		twflag = false;
	End;
	If twflag = false and (close < open) and (high - open) >= ((open - close) * wickLIMIT) and ((high - open) >= ((close - low) * (wickLIMIT + 1))) then Begin
		topWICK = true;
	End
	Else Begin
		If twflag = false then begin
			topWICK = false;
		End;
	End;
	
	//Identifies if the bottom wick is substantial
	If (close > open) and ((open - low) >= ((close - open) * wickLIMIT)) and ((open - low) >= ((high - close) * (wickLIMIT + 1))) then Begin
		bottomWICK = true;
		bwflag = true;
	End
	Else Begin
		bottomWICK = false;
		bwflag = false;
	End;
	If bwflag = false and (close < open) and ((close - low) >= ((open - close) * wickLIMIT)) and ((close - low) >= ((high - open) * (wickLIMIT + 1))) then Begin
		bottomWICK = true;
	End
	Else Begin
		If bwflag = false then begin
			bottomWICK = false;
		End;
	End;
	
	//Both wicks can't be the best
	If topWICK = true and bottomWICK = true then Begin
		topWICK = false;
		bottomWICK = false;
	End;
	
	//Constant calulcation of the take profit
	If (FirstTradeShort = true or FirstTradeLong = true) then Begin
		FLprofitTarget = ROUND(Absvalue((firstTRADEentry - EMA50)), 0);
		MAXamount = ((ROUND(Absvalue((firstTRADEentry - EMA50)), 0))/(minmove/pricescale)) * dollarPERtick;
	End;
	If (SecondTradeShort = true or SecondTradeLong = true) then Begin
		FLprofitTarget = ROUND(Absvalue((secondTRADEentry - EMA50)), 0);
		MAXamount = ((ROUND(Absvalue((secondTRADEentry - EMA50)), 0))/(minmove/pricescale)) * dollarPERtick;
	End;
		
	//Max and minimum amount of a winning or losing trade
	//MAXamount = (FLprofitTarget/(minmove/pricescale)) * dollarPERtick;
	//MINamount = -(FLstopLoss/(minmove/pricescale)) * dollarPERtick;
	If time = 1404 then begin
		//print("1404 DEBUG: ATR1=", ATR1, " ATR10=", ATR10, " ROC=", ROC, " H=", high, " C=", close, " L=", low, " O=", open, " Vol=", Ticks[0], " VolMA=", VolumeMA, " strongVOL=", strongVOL, " bottomWICK=", bottomWICK, " atrSIZEABLE=", atrSIZEABLE, " ROCinlimit=", ROCinlimit, " FirstTradeLong=", FirstTradeLong, " FirstTradeShort=", FirstTradeShort);
	End;
//-------end-------

//-------Short Trades ENTRY-------
If itsaHoliday = false and itsaWeekday = true and openbell = true and FirstTradeLong = false and FirstTradeShort = false {and dailyLOSScount < dailyLOSScountLIMIT} then Begin
	
	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true then begin
	
		firstTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTS;
		firstTRADEtime = time;
		FirstTradeShort = true;
		//print(formattedDate, ": ", time);
		print(firstTRADEentry);
		print(firstTRADEtime," FTS");
		
		//Sets the profit and loss amounts based on the ATR
		//FLprofitTarget = ROUND(Absvalue((EMA50 - close)), 0);
		FLstopLoss     = ROUND((ATR10 * lossMULT), 0) * (minmove/pricescale);
		
		If FLstopLoss > FLstopLossLIMIT then Begin
			FLstopLoss = FLstopLossLIMIT;
		End;
	
		//Max and minimum amount of a winning or losing trade
		//MAXamount = (FLprofitTarget/(minmove/pricescale)) * dollarPERtick;
		MINamount = -(FLstopLoss/(minmove/pricescale)) * dollarPERtick;
		
	End;

End;
If itsaHoliday = false and itsaWeekday = true and openbell = true and SecondTradeLong = false and SecondTradeShort = false {and dailyLOSScount < dailyLOSScountLIMIT} and FTLexited = true or FTSexited = true then Begin

	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true then begin
	
		secondTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTS;
		secondTRADEtime = time;	
		SecondTradeShort = true;
		//print(formattedDate, ": ", time);
		print(secondTRADEentry);
		print(secondTRADEtime," STS");
		
		//Sets the profit and loss amounts based on the ATR
		//FLprofitTarget = ROUND(Absvalue((EMA50 - close)), 0);
		FLstopLoss     = ROUND((ATR10 * lossMULT), 0) * (minmove/pricescale);
		
		If FLstopLoss > FLstopLossLIMIT then Begin
			FLstopLoss = FLstopLossLIMIT;
		End;
	
		//Max and minimum amount of a winning or losing trade
		//MAXamount = (FLprofitTarget/(minmove/pricescale)) * dollarPERtick;
		MINamount = -(FLstopLoss/(minmove/pricescale)) * dollarPERtick;
		
	End;

End;
//-------end-------

//-------Long Trades ENTRY-------
If itsaHoliday = false and itsaWeekday = true and openbell = true and FirstTradeLong = false and FirstTradeShort = false {and dailyLOSScount < dailyLOSScountLIMIT} then Begin
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true then begin
	
		firstTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTL;
		firstTRADEtime = time;
		FirstTradeLong = true;
		//print(formattedDate, ": ", time);
		print(firstTRADEentry);
		print(firstTRADEtime," FTL");
		
		//Sets the profit and loss amounts based on the ATR
		//FLprofitTarget = ROUND(Absvalue((EMA50 - close)), 0);
		FLstopLoss     = ROUND((ATR10 * lossMULT), 0) * (minmove/pricescale);
		
		If FLstopLoss > FLstopLossLIMIT then Begin
			FLstopLoss = FLstopLossLIMIT;
		End;
	
		//Max and minimum amount of a winning or losing trade
		//MAXamount = (FLprofitTarget/(minmove/pricescale)) * dollarPERtick;
		MINamount = -(FLstopLoss/(minmove/pricescale)) * dollarPERtick;
		
	End;

End;
If itsaHoliday = false and itsaWeekday = true and openbell = true and SecondTradeLong = false and SecondTradeShort = false {and dailyLOSScount < dailyLOSScountLIMIT} and FTLexited = true or FTSexited = true then Begin
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true then begin
	
		secondTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTL;
		secondTRADEtime = time;
		SecondTradeLong = true;
		//print(formattedDate, ": ", time);
		print(secondTRADEentry);
		print(secondTRADEtime," STL");
		
		//Sets the profit and loss amounts based on the ATR
		//FLprofitTarget = ROUND(Absvalue((EMA50 - close)), 0);
		FLstopLoss     = ROUND((ATR10 * lossMULT), 0) * (minmove/pricescale);
		
		If FLstopLoss > FLstopLossLIMIT then Begin
			FLstopLoss = FLstopLossLIMIT;
		End;
	
		//Max and minimum amount of a winning or losing trade
		//MAXamount = (FLprofitTarget/(minmove/pricescale)) * dollarPERtick;
		MINamount = -(FLstopLoss/(minmove/pricescale)) * dollarPERtick;
		
	End;

End;
//-------end-------

//-------Entry Notifications-------
If FirstTradeLong = true and FTLentered = false then Begin
	
	If low <= firstTRADEentry then Begin
		FTLentered = true;
		print("FTL ACTIVE ",time);
	End;
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (firstTRADEtime + 1) then begin
		firstTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTL;
		firstTRADEtime = time;
		FirstTradeLong = true;
		print("{keeping FTL at FTL} new entry: ", firstTRADEentry," new time: ", time);
	End
	Else Begin
	End;
	
	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (firstTRADEtime + 1) then begin
		firstTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTS;;
		firstTRADEtime = time;
		FirstTradeShort = true;
		FirstTradeLong = false;
		print("{changing FTL to FTS} new entry: ", firstTRADEentry," new time: ", time);
	End
	Else Begin
	End;
									
End;
If SecondTradeLong = true and STLentered = false then Begin
	
	If low <= secondTRADEentry then Begin
		STLentered = true;
		print("STL ACTIVE ",time);
	End;
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (secondTRADEtime + 1) then begin
		secondTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTL;
		secondTRADEtime = time;
		SecondTradeLong = true;
		print("{keeping STL at STL} new entry: ", secondTRADEentry," new time: ", time);
	End
	Else Begin
	End;
	
	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (secondTRADEtime + 1) then begin
		secondTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTS;
		secondTRADEtime = time;
		SecondTradeShort = true;
		SecondTradeLong = false;
		print("{changing STL to STS} new entry: ", secondTRADEentry," new time: ", time);
	End
	Else Begin
	End;
								
End;
If FirstTradeShort = true and FTSentered = false then Begin;
	
	If high >= firstTRADEentry then Begin
		FTSentered = true;
		print("FTS ACTIVE ",time);
	End;
	
	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (firstTRADEtime + 1) then begin
		firstTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTS;
		firstTRADEtime = time;
		FirstTradeShort = true;
		print("{keeping FTS at FTS} new entry: ", firstTRADEentry," new time: ", time);
	End
	Else Begin
	End;
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (firstTRADEtime + 1) then begin
		firstTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipFTL;
		firstTRADEtime = time;
		FirstTradeLong = true;
		FirstTradeShort = false;
		print("{changing FTS to FTL} new entry: ", firstTRADEentry," new time: ", time);
	End
	Else Begin
	End;
						
End;
If SecondTradeShort = true and STSentered = false then Begin;
	
	If high >= secondTRADEentry then Begin
		STSentered = true;
		print("STS ACTIVE ",time);
	End;
	
	If topWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (secondTRADEtime + 1) then begin
		secondTRADEentry = high + (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTS;
		secondTRADEtime = time;
		SecondTradeShort = true;
		print("{keeping STS at STS} new entry: ", secondTRADEentry," new time: ", time);
	End
	Else Begin
	End;
	
	If bottomWICK = true and strongVOL = true and ROCinlimit = true and atrSIZEABLE = true and time >= (secondTRADEtime + 1) then begin
		secondTRADEentry = low - (ROUND(Absvalue((EMA50 - close)), 0)/4) + slipSTL;
		secondTRADEtime = time;
		SecondTradeLong = true;
		SecondTradeShort = false;
		print("{changing STS to STL} new entry: ", secondTRADEentry," new time: ", time);
	End
	Else Begin
	End;
							
End;
//-------end-------

//-------DOUBLE ENTRY CHECK-------

	If FTLentered = true and FTLexited = false and time >= (firstTRADEtime + 1) then begin
	
		If (high >= (firstTRADEentry + FLprofitTarget)) and (low <= (firstTRADEentry - FLstopLoss)) then Begin
		PLamount = MINamount;
		print("FTL");
		print(0);
		reportsent = true;
		FTLexited = true;
		End;
		
		//FLdoublecheck = true;
	End;
	
	If FTSentered = true and FTSexited = false and time >= (firstTRADEtime + 1) then begin
	
		If (high >= (firstTRADEentry + FLstopLoss)) and (low <= (firstTRADEentry - FLprofitTarget)) then Begin
		PLamount = MINamount;
		print("FTS");
		print(0);
		reportsent = true;
		FTSexited = true;
		End;
		
		//FLdoublecheck = true;
	End;
	
	If STLentered = true and STLexited = false and time >= (secondTRADEtime + 1) then begin
	
		If (high >= (firstTRADEentry + FLprofitTarget)) and (low <= (firstTRADEentry - FLstopLoss)) then Begin
		PLamount = MINamount;
		print("STL");
		print(0);
		reportsent = true;
		STLexited = true;
		End;
		
		//SLdoublecheck = true;
	End;
	
	If STSentered = true and STSexited = false and time >= (secondTRADEtime + 1) then begin
	
		If (high >= (firstTRADEentry + FLstopLoss)) and (low <= (firstTRADEentry - FLprofitTarget)) then Begin
		PLamount = MINamount;
		print("STS");
		print(0);
		reportsent = true;
		STSexited = true;
		End;
		
		//SLdoublecheck = true;
	End;

//-----double entry end-------

//-------Profit and Stop Targets of First Line Long-------
If FTLentered = true and FTLexited = false and time >= (firstTRADEtime + 1) then begin

	If high >= (firstTRADEentry + FLprofitTarget) then Begin
		PLamount = MAXamount;
		print("FTL");
		print(PLamount - comCOST);
		reportsent = true;
		FTLexited = true;
		print(time,"FTL exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (high > firstTRADEentry) and FTLexited = false and time = 1600 then Begin
		print("FTL");
		print((((firstTRADEentry - close)/(minmove/pricescale)) * dollarPERtick) - comCOST);
		FTLexited = true;
	End;
	
End;

If FTLentered = true and FTLexited = false and time >= (firstTRADEtime + 1) then begin

	If low <= (firstTRADEentry - FLstopLoss) then Begin
		PLamount = MINamount;
		print("FTL");
		print(PLamount - comCOST - st_slipFTL);
		reportsent = true;
		FTLexited = true;
		dailyLOSScount = dailyLOSScount + 1;
		print(time,"FTL exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (low <= firstTRADEentry) and FTLexited = false and time = 1600 then Begin
		print("FTL");
		print((((firstTRADEentry - close)/(minmove/pricescale)) * (dollarPERtick * -1)) - comCOST - st_slipFTL);
		FTLexited = true;
		dailyLOSScount = dailyLOSScount + 1;
	End;
	
End;
//-------FTL End-------

//-------Profit and Stop Targets of Second Line Long-------
If STLentered = true and STLexited = false and time >= (secondTRADEtime + 1) then begin

	If high >= (secondTRADEentry + FLprofitTarget) then Begin
		PLamount = MAXamount;
		print("STL");
		print(PLamount - comCOST);
		reportsent = true;
		STLexited = true;
		print(time,"STL exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (high > secondTRADEentry) and STLexited = false and time = 1600 then Begin
		print("STL");
		print((((secondTRADEentry - close)/(minmove/pricescale)) * dollarPERtick) - comCOST);
		STLexited = true;
	End;
	
End;

If STLentered = true and STLexited = false and time >= (secondTRADEtime + 1) then begin

	If low <= (secondTRADEentry - FLstopLoss) then Begin
		PLamount = MINamount;
		print("STL");
		print(PLamount - comCOST - st_slipSTL);
		reportsent = true;
		STLexited = true;
		dailyLOSScount = dailyLOSScount + 1;
		print(time,"STL exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (low <= secondTRADEentry) and STLexited = false and time = 1600 then Begin
		print("STL");
		print((((secondTRADEentry - close)/(minmove/pricescale)) * (dollarPERtick * -1)) - comCOST - st_slipSTL);
		STLexited = true;
		dailyLOSScount = dailyLOSScount + 1;
	End;
	
End;
//-------STL End-------

//-------Profit and Stop Targets of First Line Short-------
If FTSentered = true and FTSexited = false and time >= (firstTRADEtime + 1) then begin

	If high >= (firstTRADEentry + FLstopLoss) then Begin
		PLamount = MINamount;
		print("FTS");
		print(PLamount - comCOST - st_slipFTS);
		reportsent = true;
		FTSexited = true;
		dailyLOSScount = dailyLOSScount + 1;
		print(time,"FTS exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (high > firstTRADEentry) and FTSexited = false and time = 1600 then Begin
		print("FTS");
		print((((close - firstTRADEentry)/(minmove/pricescale)) * (dollarPERtick * -1)) - comCOST - st_slipFTS);
		FTSexited = true;
		dailyLOSScount = dailyLOSScount + 1;
	End;
	
End;

If FTSentered = true and FTSexited = false and time >= (firstTRADEtime + 1) then begin

	If low <= (firstTRADEentry - FLprofitTarget) then Begin
		PLamount = MAXamount;
		print("FTS");
		print(PLamount - comCOST);
		reportsent = true;
		FTSexited = true;
		print(time,"FTS exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (low <= firstTRADEentry) and FTSexited = false and time = 1600 then Begin
		print("FTS");
		print((((firstTRADEentry - close)/(minmove/pricescale)) * dollarPERtick) - comCOST);
		FTSexited = true;
	End;
		
End;
//-------FTS End-------

//-------Profit and Stop Targets of Second Line Short-------
If STSentered = true and STSexited = false and time >= (secondTRADEtime + 1) then begin

	If high >= (secondTRADEentry + FLstopLoss) then Begin
		PLamount = MINamount;
		print("STS");
		print(PLamount - comCOST - st_slipSTS);
		reportsent = true;
		STSexited = true;
		dailyLOSScount = dailyLOSScount + 1;
		print(time,"STS exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;
	
	//Closes any open positions at 1600
	If (high > secondTRADEentry) and STSexited = false and time = 1600 then Begin
		print("STS");
		print((((close - secondTRADEentry)/(minmove/pricescale)) * (dollarPERtick * -1)) - comCOST - st_slipSTS);
		STSexited = true;
		dailyLOSScount = dailyLOSScount + 1;
	End; 
	
End;

If STSentered = true and STSexited = false and time >= (secondTRADEtime + 1) then begin

	If low <= (secondTRADEentry - FLprofitTarget) then Begin
		PLamount = MAXamount;
		print("STS");
		print(PLamount - comCOST);
		reportsent = true;
		STSexited = true;
		print(time,"STS exit");
		print("Profit in Ticks: ", FLprofitTarget/0.25);
		//print("Current EMA50 ", EMA50);
	End;	
	
	//Closes any open positions at 1600
	If (low <= secondTRADEentry) and STSexited = false and time = 1600 then Begin
		print("STS");
		print((((secondTRADEentry - close)/(minmove/pricescale)) * dollarPERtick) - comCOST);
		STSexited = true;
	End; 
	
End;
//-------STS End-------

//-------Continue Trading If Two Losses Have NOT Happened -------

	{If (STLexited = true or STSexited = true) and dailyLOSScount < dailyLOSScountLIMIT and openbell = true then Begin
	
		FirstTradeLong = false;
		SecondTradeLong = false;
		FirstTradeShort = false;
		SecondTradeShort = false;
		
		FTLentered = false;
		STLentered = false;
		FTSentered = false;
		STSentered = false;
		
		FTLexited = false;
		STLexited = false;
		FTSexited = false;
		STSexited = false;
	
	End;}

//-------End Continue-------

//-------Resets all variables for the next trading day-------

If time = 1600 then Begin

	If FirstTradeLong = false and FirstTradeShort = false and SecondTradeLong = false and SecondTradeShort = false and notrade = false then Begin
		//print(formattedDate, ": ", time);
		Print("No Trade");
		//Print("0");
		notrade = true;
	End;
	
	If FTLentered = false then Begin
		FTLentered = true;
	End;
	
	If STLentered = false then Begin
		STLentered = true;
	End;
	
	If FTSentered = false then Begin
		FTSentered = true;
	End;
	
	If STSentered = false then Begin
		STSentered = true;
	End;
	

	itsaWeekday = true;
	itsaHoliday = false;
	
	FTSnotif = false;
	
	FirstTradeLong = false;
	FirstTradeShort = false;
	SecondTradeLong = false;
	SecondTradeShort = false;
	
	FTLentered = false;
	FTLexited = false;
	FTSentered = false;
	FTSexited = false;
	STLentered = false;
	STLexited = false;
	STSentered = false;
	STSexited = false;
	
	FTLconfirm = false;
	STLconfirm = false;
	FTSconfirm = false;
	STSconfirm = false;
	
	reportsent = false;
	
	flag1 = false;
	flag2 = false;
	
	notrade = false;
	
	FTLnotfilled = false;
	FTSnotfilled = false;
	STLnotfilled = false;
	STSnotfilled = false;
	
	dailyLOSScount = 0;
	
	openbell = false;
End;

if time = 1801 and itsaHoliday = true then Begin

	If FirstTradeLong = false and FirstTradeShort = false and SecondTradeLong = false and SecondTradeShort = false and notrade = false then Begin
		//print(formattedDate, ": ", time);
		Print("No Trade");
		//Print("0");
		notrade = true;
	End;
	
	If FTLentered = false then Begin
		FTLentered = true;
	End;
	
	If STLentered = false then Begin
		STLentered = true;
	End;
	
	If FTSentered = false then Begin
		FTSentered = true;
	End;
	
	If STSentered = false then Begin
		STSentered = true;
	End;
	

	itsaWeekday = true;
	itsaHoliday = false;
	
	FTSnotif = false;
	
	FirstTradeLong = false;
	FirstTradeShort = false;
	SecondTradeLong = false;
	SecondTradeShort = false;
	
	FTLentered = false;
	FTLexited = false;
	FTSentered = false;
	FTSexited = false;
	STLentered = false;
	STLexited = false;
	STSentered = false;
	STSexited = false;
	
	FTLconfirm = false;
	STLconfirm = false;
	FTSconfirm = false;
	STSconfirm = false;
	
	reportsent = false;
	
	flag1 = false;
	flag2 = false;
	
	notrade = false;
	
	FTLnotfilled = false;
	FTSnotfilled = false;
	STLnotfilled = false;
	STSnotfilled = false;
	
	dailyLOSScount = 0;
	
	openbell = false;

End;
//-------end-------
