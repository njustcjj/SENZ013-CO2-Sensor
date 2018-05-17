# SENZ013 CO<SUB>2</SUB>传感器

###### 翻译

> `英文` 请参考 [`这里`](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/README.md)

> `中文` 请参考 [`这里`](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/README_CN.md)

![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013.jpg "SENZ013")
 

### 产品介绍

> SENZ013 CO2传感器具有双路信号输出，模拟量信号和TTL信号。当CO2浓度增高，探头输出的模拟电压值就越高。
用户还可以用板子上的电位器直接设置阈值，当CO2浓度高达一定程度时，探头旁边的针头会输出低电平信号。

> 该模块采用工业级的MG-811 CO2探头，对CO2极为敏感，同时还能排除酒精和CO的干扰。该探头对环境温湿度的依赖小，性能稳定，快速恢复响应。模块还带有温度补偿设计和信号放大电路，进一步提高准确度和灵敏度。

> 用途：家庭CO2浓度监控及报警

### 产品参数

- 工作电压：+ 6V
- 工作电流：<10mA
- 工作功耗：1200mW
- 尺寸：32 x 22 x 30mm
- 充分预热：24h


### 使用教程

#### 引脚定义

|Sensor pin|Ardunio Pin|Function Description|
|-|:-:|-|
|VCC|3.3V~5V|Power|
|GND|GND||
|Dout|Digital pin|TTL signal output|
|Aout|Analog pin|Analog signal output|
|Tcm|Analog pin|Temperature compensation output|


![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013_pin.jpg "引脚定义") 


#### 连线图

![](https://github.com/njustcjj/SENZ013-CO2-Sensor/blob/master/pic/SENZ013_connect.png "连线图") 


### 示例代码

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


### 购买[*SENZ013 CO<SUB>2</SUB>传感器*](https://www.ebay.com/).