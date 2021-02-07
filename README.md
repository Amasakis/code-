#include <Trade\Trade.mqh>
#include "OpenBuyOrder.mq5";
#include "OpenSellOrder.mq5";
double OpenPositionVolume=0;
double LastEquity=0;
double Ask=0;
double Bid=0;
double Equity=0;
double Balance=0;
CTrade trade;




void OnTick()
{

   uchar Position_Count_sell = 0;
   uchar Position_Count_buy = 0;

  
   Balance=AccountInfoDouble(ACCOUNT_BALANCE);
   
   // Get the equity
   Equity=AccountInfoDouble(ACCOUNT_EQUITY);
   
   // Choose the current currency pair
   PositionSelect(Symbol());
   
   // Get the current position volue
   OpenPositionVolume =(PositionGetDouble(POSITION_VOLUME)); 
   
   // Get the ask price
   Ask=NormalizeDouble(SymbolInfoDouble(_Symbol,SYMBOL_ASK),_Digits);
   
   // get the bid price
   Bid=NormalizeDouble(SymbolInfoDouble(_Symbol,SYMBOL_BID),_Digits);
   
    Comment ("\n","Balance: ",Balance,"\n","Equity: ",Equity,"\n","OpenPositionVolume: ", OpenPositionVolume, "\n", "LastEquity: ",LastEquity);
    	 
      // create an empty string for the signal;
      string signal="";
      
      // create an Array for several prices
      double myMovingAverageArray1[],myMovingAverageArray2[],myMovingAverageArray3[],myMovingAverageArray4[];
      
      // define the properties of the Moving Average1
      int movingAverageDefinition1 = iMA (_Symbol,_Period,50,0,MODE_EMA,PRICE_CLOSE);
      
      // define the properties of the Moving Average2
      int movingAverageDefinition2 = iMA (_Symbol,_Period,100,0,MODE_EMA,PRICE_CLOSE);
      
      // define the properties of the Moving Average3
      int movingAverageDefinition3 = iMA (_Symbol,_Period,150,0,MODE_EMA,PRICE_CLOSE);
      
      // define the properties of the Moving Average4
      int movingAverageDefinition4 = iMA (_Symbol,_Period,200,0,MODE_EMA,PRICE_CLOSE);      
      
      // sort the price array1 from the current candle downwards
      ArraySetAsSeries(myMovingAverageArray1,true);
      
      // sort the price array2 from the current candle downwards
      ArraySetAsSeries(myMovingAverageArray2,true);
      
      // sort the price array1 from the current candle downwards
      ArraySetAsSeries(myMovingAverageArray3,true);
      
      // sort the price array2 from the current candle downwards
      ArraySetAsSeries(myMovingAverageArray4,true);      
      
      // Defined MA1, one line,current candle,3 candles, store result 
      CopyBuffer(movingAverageDefinition1,0,0,3,myMovingAverageArray1);
      
      // Defined MA2, one line,current candle,3 candles, store result 
      CopyBuffer(movingAverageDefinition2,0,0,3,myMovingAverageArray2);
      
      // Defined MA3, one line,current candle,3 candles, store result 
      CopyBuffer(movingAverageDefinition3,0,0,3,myMovingAverageArray3);
      
      // Defined MA4, one line,current candle,3 candles, store result 
      CopyBuffer(movingAverageDefinition4,0,0,3,myMovingAverageArray4);      
      
      // calculate MA1 for the current candle
      double myMovingAverageValue1=myMovingAverageArray1[0];
      
      // calculate MA2 for the current candle
      double myMovingAverageValue2=myMovingAverageArray2[0];
      
      // calculate MA3 for the current candle
      double myMovingAverageValue3=myMovingAverageArray3[0];
      
      // calculate MA4 for the current candle
      double myMovingAverageValue4=myMovingAverageArray4[0];
      
       
      // Sell Signal
      if (myMovingAverageValue1 < myMovingAverageValue2)
      if (myMovingAverageValue2 < myMovingAverageValue3)
      if (myMovingAverageValue3 < myMovingAverageValue4)      
            {
                    if(Position_Count_sell==0)
                    if(Position_Count_buy < 1)
                /*  else if (Magic()=00002)
     trade.SetExpertMagicNumber(00001);  */
     trade.SetExpertMagicNumber(00001);            
	  OpenSellOrder();
     
        for(int i=PositionsTotal()-1; i>=0; i--)
     {
      ulong  Position_Symbol = PositionGetSymbol(i);
      ulong  Position_Magic_Number = PositionGetInteger(POSITION_MAGIC);
         
      if(Position_Magic_Number == 00001)
      Position_Count_sell+=1;
     }       
            }
            
                        
                
      // Buy Signal
      if (myMovingAverageValue1 > myMovingAverageValue2)
      if (myMovingAverageValue2 > myMovingAverageValue3)
      if (myMovingAverageValue3 > myMovingAverageValue4)      
            {
                 if(Position_Count_buy==0)
                 if(Position_Count_sell < 1)
            /*   else if (MagicNumber=00001)
     trade.SetExpertMagicNumber(00002);  */
     trade.SetExpertMagicNumber(00002);        
	  OpenBuyOrder();
	     
	     for(int i=PositionsTotal()-1; i>=0; i--)
     {
      ulong  Position_Symbol = PositionGetSymbol(i);
      ulong  Position_Magic_Number = PositionGetInteger(POSITION_MAGIC);
         
      if(Position_Magic_Number == 00002)
      Position_Count_buy+=1;
     }
	  
            } 
                     
            
      Comment ("Signal: ",signal,"\n",
               "myMovingAverageValue1: ",myMovingAverageValue1,"\n",
               "myMovingAverageValue2: ",myMovingAverageValue2,"\n",
               "myMovingAverageValue3: ",myMovingAverageValue3,"\n",  
               "myMovingAverageValue4: ",myMovingAverageValue4,"\n"                                           
              );
}  
