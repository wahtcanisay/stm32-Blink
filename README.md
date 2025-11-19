## 理论知识
选用stm32的PC13和PA9进行实验  
PC13接板载led
PA9外接限流电阻与led
![电路接线图](https://github.com/wahtcanisay/stm32-Blink/blob/master/%E9%9D%A2%E5%8C%85%E6%9D%BF%E7%94%B5%E8%B7%AF%E6%8E%A5%E7%BA%BF%E5%9B%BE.png)

**PC13采用开漏接法，写0输出低电平led亮；写1输出高阻态，led灭**  
**PA9采用推挽接法，写0输出低电平led灭；写1输出高电平，led亮**  

## 软件实现
### cubemx部分
在cubemx中新建工程，选用STM32F103C8T6型号，在`systemcore`中选中`sys`，将PC13与PA9左键选中设定为GPIO_output
![Cubemx_GPIO_Output](https://github.com/wahtcanisay/stm32-Blink/blob/master/Cubemx_GPIO_Output.png)

随后在左侧选中`GPIO`  
在PC13引脚中将`output level`选`High`，代表初始输出高电平，`mode`中选用`Output Open Drain`表示选用开漏模式  
在PA9引脚中将`output level`选`Low`，代表初始输出低电平，`mode`中选用`Output Psuh Pull`表示选用推挽模式  
### keil5代码实现
```c
void HAL_GPIO_WritePin(GPIOX, GPIO_PIN, Pin_State)
```
1.GPIOX 代表组编号，A->D  
2.GPIO_PIN 代表引脚编号，0->15  
3.Pin_State 代表写入值 ，0/1 分别对应`GPIO_Pin_Reset`/`GPIO_Pin_Set`  

```C
void HAL_Delay(uint32_t Delay)
```

Delay 代表延迟时间，单位为ms，故0.5s要键入500  

**板载与外接LED每隔0.5s闪烁一次具体过程**
```C
while (1)
  {
		/*LED点亮*/
		HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_RESET); /*PC13置0*/
		HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_SET);    /*PA9置1*/
		
		HAL_Delay(500);
		
		/*LED熄灭*/
		HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_SET);   /*PC13置1*/
		HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_RESET);  /*PA9置0*/
		
		HAL_Delay(500);
  }
```
