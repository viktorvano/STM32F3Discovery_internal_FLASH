# STM32F3Discovery_internal_FLASH
 STM32 writing and reading internal FLASH and bootloader  
  
  
How to jump to App as a Bootloader?  

Just like this:  

- write your App starting address: #define APP_START (0x08005000)

void jump_to_app()  
{  
	//-- jump to application image: new SP and reset vector address  
	volatile uint32_t *uwImagePtrW;  
	volatile uint32_t newMSP, jmpAddr;  
  
	uwImagePtrW = (uint32_t *)APP_START;  
	newMSP = *uwImagePtrW;  
	uwImagePtrW++;  
	jmpAddr = *uwImagePtrW;  

	//-- reset peripherals to guarantee flawless start of user application  
	HAL_GPIO_DeInit(GPIOA, GPIO_PIN_0);  
	HAL_TIM_Base_Stop_IT(&htim2);  
	HAL_TIM_Base_DeInit(&htim2);  
	SysTick->CTRL &= ~SysTick_CTRL_ENABLE_Msk;  
	HAL_RCC_DeInit();  
	HAL_DeInit();  

	/* let's do The Jump! */  
	__set_MSP(newMSP);  
	asm("bx %0  n" : : "r" (jmpAddr));  
}  

Done!!!  