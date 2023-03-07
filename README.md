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
For debugg and IDE configuration, first connect your board to the computer and then do the following steps :

1- Build the project.

2- Verify the debug configuration. make sure that SWV is selected. So, if you don't select this then your IDE will not capture the message coming over SWO line.

3- Debug the code.

4- Go to window, show view, SWV --> ITM data console. open this.

5- Click on configure trace. ITM stimulus ports --> select port 0, because the ITM SendChar function is going to write into the ITM stimulus port 0.So, there are up to 32 ports, but we are making use of the 0th port. Once you select that, click OK.

6- start the trace (the red circle icone).

7-run the code (Resume because we are in debugger mode)



That it we get the message 'Hello world'. So, printf is actually an excellent option to debug your code.
So, when you are writing your embedded code in case if you want to use printf to get the debug messages, then definitely you can make use of this feature of the processor.

