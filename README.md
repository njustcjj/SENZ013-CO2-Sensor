# SENZ013-CO<sub>2</sub>-Sensor

###### Translation

> For `English`, please click [`here.`](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/README.md)

> For `Chinese`, please click [`here.`](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/README_CN.md)

![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013.jpg "SENZ013")


### Introduction

>  SENZ013 CO2 sensor has twin channel output, analog and TTL signal. The analog output voltage of the module increases as the concentration of the CO2 increases. The potentiometer onboard is designed to set the threshold of voltage. Once the CO2 concentration is high enough (voltage is higher than threshold), a digital signal (low) will be released.

>It has MG-811 gas sensor onboard which is highly sensitive to CO2 and less sensitive to alcohol and CO, Low humidity&temperature dependency. All components have industrial quality which means stability and reproducibility. This sensor also has an onboard conditioning circuit for amplifying output signal and temperature compensation circuit to get high accurate data.
>
> Usage : Alarm and monitor the CO<sub>2</sub> concentration in home.


### Specification

- Operating voltage: +6V
- Operating current: <10mA
- Operating power dissipation: 1200mW
- Size:32 x 22 x 30 mm
- Complete preheat: 24h


### Tutorial

#### Wire Definition

|Sensor pin|Ardunio Pin|Function Description|
|-|:-:|-|
|VCC|3.3V~5V|Power|
|GND|GND||
|Dout|Digital pin|TTL signal output|
|Aout|Analog pin|Analog signal output|
|Tcm|Analog pin|Temperature compensation output|

![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013_pin.jpg "Pin Definition") 

#### Connecting Diagram

![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013_connect.png "Connecting Diagram") 

#### Sample Code


	/************************Hardware Related Macros************************************/
	#define         MG_PIN                       (A0)     //define which analog input channel you are going to use
	#define         BOOL_PIN                     (2)
	#define         DC_GAIN                      (8.5)   //define the DC gain of amplifier

	/***********************Software Related Macros************************************/
	#define         READ_SAMPLE_INTERVAL         (50)    //define how many samples you are going to take in normal operation
	#define         READ_SAMPLE_TIMES            (5)     //define the time interval(in milisecond) between each samples in 
                                                     //	normal operation

	/**********************Application Related Macros**********************************/
	//These two values differ from sensor to sensor. user should derermine this value.
	#define         ZERO_POINT_VOLTAGE           (0.220) //define the output of the sensor in volts when the concentration of CO2 is 400PPM
	#define         REACTION_VOLTGAE             (0.030) //define the voltage drop of the sensor when move the sensor from air into 1000ppm CO2

	/*****************************Globals***********************************************/
	float           CO2Curve[3]  =  {2.602,ZERO_POINT_VOLTAGE,(REACTION_VOLTGAE/(2.602-3))};   
                                                     //two 	points are taken from the curve. 
                                                     //	with these two points, a line is formed which is
                                                     //"ap	proximately equivalent" to the original curve.
                                                     //	data format:{ x, y, slope}; point1: (lg400, 0.324), 	point2: (lg4000, 0.280) 
                                                     //	slope = ( reaction voltage ) / (log400 –log1000) 

	void setup()
	{
	    Serial.begin(9600);                              //UART setup, baudrate = 9600bps
	    pinMode(BOOL_PIN, INPUT);                        //set pin to input
	    digitalWrite(BOOL_PIN, HIGH);                    //turn on pullup resistors

	   Serial.print("MG-811 Demostration\n");                
	}

	void loop()
	{
	    int percentage;
	    float volts;

	    volts = MGRead(MG_PIN);
	    Serial.print( "SEN0159:" );
	    Serial.print(volts); 
	    Serial.print( "V           " );

	    percentage = MGGetPercentage(volts,CO2Curve);
	    Serial.print("CO2:");
	    if (percentage == -1) {
	        Serial.print( "<400" );
	    } else {
	        Serial.print(percentage);
	    }

	    Serial.print( "ppm" );  
	    Serial.print("\n");

	    if (digitalRead(BOOL_PIN) ){
	        Serial.print( "=====BOOL is HIGH======" );
	    } else {
	        Serial.print( "=====BOOL is LOW======" );
	    }

	    Serial.print("\n");

	    delay(500);
	}

	/*****************************  MGRead *********************************************
	Input:   mg_pin - analog channel
	Output:  output of SEN-000007
	Remarks: This function reads the output of SEN-000007
	************************************************************************************/ 
	float MGRead(int mg_pin)
	{
	    int i;
	    float v=0;

	    for (i=0;i<READ_SAMPLE_TIMES;i++) {
	        v += analogRead(mg_pin);
	        delay(READ_SAMPLE_INTERVAL);
	    }
	    v = (v/READ_SAMPLE_TIMES) *5/1024 ;
	    return v;  
	}

	/*****************************  MQGetPercentage **********************************
	Input:   volts   - SEN-000007 output measured in volts
	         pcurve  - pointer to the curve of the target gas
	Output:  ppm of the target gas
	Remarks: By using the slope and a point of the line. The x(logarithmic value of ppm) 
	         of the line could be derived if y(MG-811 output) is provided. As it is a 
	         logarithmic coordinate, power of 10 is used to convert the result to non-logarithmic 
	         value.
	************************************************************************************/ 
	int  MGGetPercentage(float volts, float *pcurve)
	{
	   if ((volts/DC_GAIN )>=ZERO_POINT_VOLTAGE) {
	      return -1;
	   } else { 
	      return pow(10, ((volts/DC_GAIN)-pcurve[1])/pcurve[2]+pcurve[0]);
	   }
	}


### Purchasing [*SENZ013 CO2 Sensor*](https://www.ebay.com/).
