# ITM_sendData
This is the implementation of ITM send character.
So, this is the implementation of printf like feature using ARM Cortex M3/M4/M7 ITM functionality.
This function will not work for ARM Cortex M0/M0+. If you are using Cortex M0, then you can use semihosting feature of openOCD.

This is the working principle behind SWV (Serial Wire Viewer), a which is used for tracing functionality.

We have to make some changes to our project in order to include the code a which actually writes into the ITM FIFO. So copy the itm_send_data.c Code and go to the STM32CubeIDE then go to syscalls.c and paste the code after the Includes. After that we need to copy the function name "ITM_SendChar" and go to the write function in syscalls.c comment the out and use our function ITM_SendChar. so it look like this :

__attribute__((weak)) int _write(int file, char *ptr, int len)
{
  (void)file;
  int DataIdx;

  for (DataIdx = 0; DataIdx < len; DataIdx++)
  {
//    __io_putchar(*ptr++);
	  ITM_SendChar(*ptr++);
  }
  return len;
}

************************************
When we call printf in main.c, the call is made to the standard library. the C standard library, where the printf function is actually implementer.

So, in the printf implementation there is a lower level call such as write, that's a low level call.

So, the standard library implementation calls write function that is actually implemented in syscalls.c.

So, standard library calls this function and the data is received in the pointer. And we are simply

diverting the pointer content to our function ITM_SendChar. this function writes into the FIFO and from FIFO it comes over the SWO line all the way back to the ST-LINK circuitry and from there it comes to our PC. And our IDE captures that message and it can print it on the console.
***********************************
For debugg cand IDE configuration,

