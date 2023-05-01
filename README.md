Download Link: https://assignmentchef.com/product/solved-eel3744c-lab-5-asynchronous-serial-communication
<br>
<h1>OBJECTIVES</h1>

<ul>

 <li>Learn how to apply asynchronous communication and the <strong>U</strong>niversal <strong>S</strong>ynchronous/<strong>A</strong>synchronous <strong>R</strong>eceiver <strong>T</strong>ransmitter (<strong>USART</strong>) system of the <em>ATxmega128A1U</em>.</li>

</ul>

<h1>INTRODUCTION</h1>

Beyond <strong>parallel communication</strong>, where multiple bits of data are transferred together via a bus of pins<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>, <strong>serial communication</strong> is another method in which a processor can communicate with external devices. Overall, serial communication involves the process of sending one bit of data at a time, while following a specific protocol.

Serial communication is generally classified as either being <strong>asynchronous </strong>or <strong>synchronous</strong>. Unlike synchronous serial communication, where a common clock signal is used to determine when to send, receive, or sample data between multiple devices, there exists no synchronization signal for asynchronous serial communication. Instead, asynchronous communication protocols rely on the hope that a common transfer rate will be upheld by any systems using a chosen protocol; if this is not achieved, data transferred or received could be wrongly interpreted, or even entirely missed.

To facilitate asynchronous serial communication, a device known as a <strong>U</strong>niversal <strong>A</strong>synchronous <strong>R</strong>eceiver/<strong>T</strong>ransmitter (<strong>UART</strong>) is generally used in conjunction with both a transmitting device and a receiving device. A UART utilizes a clock signal to generate a transfer rate, denoted as the <strong>baud rate</strong><a href="#_ftn2" name="_ftnref2"><sup>[2]</sup></a>, and also uses two physical connections to communicate data: one pin to <strong>r</strong>eceive data (<strong>R</strong>x), and one pin to <strong>t</strong>ransmit data (<strong>T</strong>x). Within the <em>ATxmega128A1U</em>, several UART modules are available by way of the <strong>U</strong>niversal <strong>S</strong>ynchronous/<strong>A</strong>synchronous <strong>R</strong>eceiver <strong>T</strong>ransmitter (<strong>USART</strong>) system.<a href="#_ftn3" name="_ftnref3"><strong><sup>[3]</sup></strong></a>

<h1>LAB STRUCTURE</h1>

Within this lab, you will begin to explore the asynchronous capabilities of the USART system within the <em>ATxmega128A1U</em>. In § 1, you will research relevant information regarding the USART system. In § 2, you will learn to transmit a text character from your microcontroller to a connected computer, via the USART system. In § 3, you will use an oscilloscope to measure the transmission signal generated from a relevant USART module. In § 4, you will learn to transmit a character string of arbitrary length<sup>4</sup>. In § 5, you will determine how to receive a character from a connected computer via the USART system. In § 6, you will learn to receive a character string of arbitrary length<strong><sup>4</sup></strong>. In § 7, you will configure the reception of data via the USART system to be interrupt-based.




<h1>REQUIRED MATERIALS                                       SUPPLEMENTAL MATERIALS</h1>

<ul>

 <li><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_XMEGA_AU_Manual.pdf">Atmel </a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_XMEGA_AU_Manual.pdf"><em>ATxmega128A1U</em></a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_XMEGA_AU_Manual.pdf"> AU Manual (doc8331)</a></li>

 <li><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf">Atmel </a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf"><em>ATxmega128A1U</em></a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf"> Manual (doc8385)</a></li>

 <li><em>OOTB µPAD v2.0</em> with USB A/B cable</li>

 <li><strong>D</strong>igilent <strong>A</strong>nalog <strong>D</strong>iscovery (<strong>DAD</strong>) kit with <em>WaveForms </em>software</li>

</ul>

<strong> </strong>

<h1>PRE-LAB PROCEDURE</h1>

<strong><u>REMINDER OF LAB POLICY</u></strong>

You must re-read the <a href="https://mil.ufl.edu/3744/admin/Lab%20Rules%20%26%20Policies.pdf"><em>Lab Rules &amp; Policies</em></a> before submitting any pre-lab assignment and before attending any lab.

<h2>1. INTRODUCTION TO USART</h2>

<table width="348">

 <tbody>

  <tr>

   <td width="348"><strong><u>PRE-LAB EXERCISES</u></strong>i.         The sampling rate of a UART receiver is usually faster than the baud rate of the overall system. Why is this so?ii.       What is the maximum possible baud rate for asynchronous communication within the USART system of the <em>ATxmega128A1U</em>, assuming that the microcontroller has a system clock frequency of 2 MHz and that the USART “double-speed mode” is disabled (i.e., the relevant bit CLK2X is set to 0)? In addition to the maximum rate, provide the values of the relevant registers used to configure that rate. Whenever appropriate, support your answer with calculations.iii.     In the context of the USART system within the <em>ATxmega128A1U</em>, how many buffers (i.e., memory locations that store temporary data) are used by a transmitter? How many are used by a receiver?Additionally, for both transmitters and receivers, explain how the use of buffers provides greater flexibility to an application involving these components.iv.     If an asynchronous serial communication protocol of 8 data bits, one start bit, one stop bit, no parity, and baud rate of 150 kHz was chosen, calculate how many seconds it would take to transmit the ASCII character string “Dr. Schwartz saw seven slick slimy snakes slowly sliding southward.” (This string has 67 characters.) Show all work.</td>

  </tr>

 </tbody>

</table>

<table width="356">

 <tbody>

  <tr>

   <td width="356"><strong>NOTE: </strong>It is said that the <em>microcontroller receives data transmitted from the EDBG chip</em>, and that the <em>EDBG receives data transmitted from the microcontroller</em>. Following this nomenclature, recognize that the signal labeled <em>EDBG_USART_CDC_TX</em> in the <em>µPAD</em> schematic is meant to correspond to the Rx signal within the relevant USART module, and the signal labeled <em>EDBG_USART_CDC_RX </em>is meant to correspond to the Tx signal within the same USART module.</td>

  </tr>

 </tbody>

</table>

In this section, you will explore documentation for the <strong>U</strong>niversal <strong>S</strong>ynchronous/<strong>A</strong>synchronous <strong>R</strong>eceiver <strong>T</strong>ransmitter (<strong>USART</strong>) system within the <em>ATxmega128A1U</em>, which provides the microcontroller with forms for both synchronous and asynchronous serial communication. However, in this lab, we will only explore the asynchronous form provided by the system.

1.1. Read any relevant parts of § 23 (<em>USART</em>) within the 8331 manual to learn about the asynchronous abilities of the USART system available within the <em>ATxmega128A1U</em>.

On the <em>µPAD</em>, the <em>ATxmega128A1U</em> uses a specific USART module to transfer data, via an EDBG chip, to and from the USB type B port also located on the <em>µPAD</em>; this allows communication between the USB type B port and a USB type A port on a computer, whenever the appropriate USB A/B cable is connected. (Note that the EDBG chip is not shown on the <em>µPAD</em> schematic due to the request of the manufacturer.)

1.2. Review the relevant <em>µPAD</em> schematic to identify which USART module is used to communicate with a connected computer.

<h2>2. USART, CHARACTER TRANSMISSION</h2>




In this section, you will write an assembly program, <strong>lab5_2.asm</strong>, to configure the appropriate USART module within your microcontroller to send data to your computer via the relevant USB ports. For this lab, you must utilize the following asynchronous serial communication protocol: odd parity, 8 data bits, 1 start bit, 1 stop bit, and 115,200 bps (bits per second) baud rate.

2.1. Create an assembly program, <strong>lab5_2.asm</strong>. In this program, first create the following two subroutines.

2.1.1. <em>USART Initialization </em>(USART_INIT). This subroutine should initialize the necessary USART module.

2.1.1.1. Set the data direction of the <em>appropriate</em> USART transmit pin.

2.1.1.2. Configure the USART module for the appropriate mode (synchronous, asynchronous, etc.), and configure the expected number of data bits, parity type, and number of stop bits.

2.1.1.3. Set the baud rate by storing the appropriate value in the relevant baud rate registers. (See Appendix B for

some information regarding baud rates.) You can use the excel workbook given on the course website <a href="https://tinyurl.com/3744-baud-calc">(</a><a href="https://tinyurl.com/3744-baud-calc">Baud Calculator</a><a href="https://tinyurl.com/3744-baud-calc">)</a> to verify any baud rate calculations, but <strong>be sure that you know how to calculate the necessary values by hand for quizzes and exams</strong>. (When configuring baud rate registers, it is useful to use assembler directives when programming in assembly, or macros when programming in C, which will be relevant later.)

2.1.2. <em>Output Character </em>(OUT_CHAR). This subroutine will output a single character to the transmit pin of a chosen USART module. It will be assumed that the relevant character is passed into the subroutine via a general-purpose register (e.g., R16 or R17).

2.1.2.1. At the beginning of this subroutine, check if there is currently an ongoing transmission in the relevant USART module; if there is, wait until it has been completed. An appropriate interrupt flag should be <em>polled</em> to handle this, i.e., do <em>not</em> use an interrupt.

2.1.2.2. Transmit the character passed into the subroutine.

2.2. Next, create a main routine within the assembly program to continually transmit the ASCII character <strong>U</strong> (i.e., the capital letter U) utilizing the OUT_CHAR subroutine.

You will need to use a serial terminal program on your computer to view any data transmitted by your microcontroller. Basic information on how to configure/use <em>PuTTY</em>, a popular serial terminal program, is given in Appendix A of this document. Some other popular terminal programs are <em>X-CTU</em>, <em>RealTerm</em>, <em>Bray Terminal</em> (also known as <em><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="387a4a7841">[email protected]</a>++ Terminal</em>), <em>MobaXterm</em>, and <em>HyperTermina</em>l. There is even a terminal within the <em>Data Visualizer</em> extension of <em>Atmel Studio</em>. You may use any serial terminal program for this course, as long as it has all features needed. (These features will not be listed here.)

<table width="735">

 <tbody>

  <tr>

   <td width="283"><strong>3.</strong> <strong>USART, MEASURING BAUD RATE</strong></td>

  </tr>

 </tbody>

</table>

2.3. Use a serial terminal program on your computer, e.g., <em>PuTTY</em>, to test your assembly program and verify that the ASCII character <strong>U</strong> is continually transmitted.




In this section of the lab, you will use your DAD to measure a baud rate created by a USART module, and then record a transmission frame for the ASCII character <strong>U</strong>.

<table width="356">

 <tbody>

  <tr>

   <td width="356"><strong>NOTE: </strong>When utilizing a separate USART module, you will <em>not</em> be able to communicate with a connected computer, since the <em>µPAD</em> was not designed to connect any other module to the relevant EDBG chip.</td>

  </tr>

 </tbody>

</table>

Unfortunately, on the <em>µPAD</em>, there is no practical way to measure the physical pins utilized by the USART module connected to your computer. Thus, so that any measurements may be easier, you will utilize some other USART module that has the relevant signals mapped to more accessible pins. routine. (Other than the different USART module, this program should be unchanged from your previous program.)

<table width="739">

 <tbody>

  <tr>

   <td width="733">transmission frame screenshot, annotate each element within one frame by type, e.g., <em>start bit, data bit 0 (D0), data bit 1 (D1)</em>, etc.3.1. Create an assembly program, <strong>lab5_3.asm</strong>, to configure <strong>NOTE: </strong>0b 01010101) was chosen The ASCII character for the above tasks to allow the <strong>U </strong>(equivalent to 0x55 = a USART module that has its Tx signal connected to an I/O elements within a character transmission frame to be easily pin that can be easily measured via the <em>µPAD</em>, and then to identified. continually transmit the ASCII character <strong>U</strong> within a main<strong>4.</strong> <strong>USART, STRING TRANSMISSION</strong>Now that you have a method to output a single character via the             4.1.1.1. Read the character pointed to by Z and increment USART system, you should be able to easily create a routine to              the pointer.<sup>output a character string of arbitrary length.                                                                    </sup>4.1.1.2. For each non-null (i.e., non-zero) character, call4.1. Create an assembly file, <strong>lab5_4.asm</strong>. First, copy the the subroutine OUT_CHAR; when a null character subroutines used in § 2. Then, write the following is found, return from the subroutine.<sup>subroutine.                                                                                                                     </sup>4.2. Create a main routine within the relevant assembly

    <table width="356">

     <tbody>

      <tr>

       <td width="356"><strong>NOTE:</strong> Recall that ASCII characters can be referenced in <em>Atmel Studio</em> individually, by using single quotes (e.g., ‘A’), and as a string, by using double quotes (e.g., “this is a string of ASCII characters”).</td>

      </tr>

     </tbody>

    </table>4.1.1. <em>Output character string </em>(OUT_STRING). This program to output your complete name, using the subroutine should output a character string stored in OUT_STRING subroutine. Use a terminal program on program memory, using the appropriate USART your computer to test your assembly program. module. When this subroutine is called, it will be assumed that the Z register already points to the beginning of a character string within memory, i.e., any main program utilizing this subroutine must properly configure the Z register before calling the subroutine.<strong>5.</strong> <strong>USART, CHARACTER INPUT</strong></td>

  </tr>

 </tbody>

</table>

3.2. To verify that the defined protocol is met (i.e., 115,200 Hz baud rate, odd parity, 8 data bits, 1 start bit, and 1 stop bit), use the <em>Scope </em>feature within <em>WaveForms</em>, along with your DAD, to measure the width of both a single data bit and a single character transmission frame. Take an appropriate screenshot for each measurement. For the character




In this section, you will begin to configure the appropriate USART module to receive serial data from your computer.

5.1. Create an assembly file, <strong>lab5_5.asm</strong>. First, copy the subroutines used in § 4, and edit the USART_INIT subroutine to additionally enable the receiver within the appropriate module and configure the data direction of the pin connected to the Rx signal. Then, write the following subroutine.

5.1.1. <em>Input character</em> (IN_CHAR). This subroutine should receive a single character with the relevant USART module and return the received character to the calling procedure via a specified general-purpose register (e.g., R16 or R17).

5.1.1.1. Check if a character has been received (by polling an appropriate interrupt flag), and if not, keep checking until one has been received.

5.1.1.2. Read the received character from the appropriate buffer and return the character to the calling procedure.

5.2. Design a main routine within the relevant assembly file to

<table width="735">

 <tbody>

  <tr>

   <td width="704">continually echo (i.e., transmit back) to your computer any<strong>6.</strong> <strong>USART, STRING INPUT</strong>

    <table width="733">

     <tbody>

      <tr>

       <td width="389">Now that you have a method to input a single character via the USART system, you should be able to create a routine to input a character string of arbitrary length.6.1. Create an assembly program (<strong>lab5_6.asm</strong>). First, copy the subroutines used in § 5. Then, write the following subroutine.6.1.1. <em>Input character string </em>(IN_STRING). This subroutine should receive a character string of arbitrary length with the relevant USART module and store the received character string to some memory location(s) within data memory via the Y index. Whenever this subroutine is called, it will be assumed that the Y index already points to the beginning of some pre-allocated contiguous memory locations, i.e., any main program utilizing this subroutine must properly configure data memory and the Y index before calling the subroutine.6.1.1.1. Continually read characters from the appropriate USART module with the IN_CHAR subroutine. For each character not equal to the carriage return<a href="#_ftn4" name="_ftnref4"><sup>[4]</sup></a> character (CR, 0x0D) character, nor the backspace character (BS, 0x08), nor the delete character (DEL, 0x7F), store the character in the next appropriate data memory location with the Y index; when a backspace character or delete6</td>

       <td width="344">allow for another character to be written, and when a carriage return character is found, store a null character at the end of the input string and return from the subroutine.6.2. Create a main routine within the relevant assembly program to input your complete name, using the IN_STRING subroutine, and then echo the relevant input string. To echo this string stored in data memory, you will not be able to use the OUT_STRING designed in § 4 (since this subroutine was designed to read from program memory), however another subroutine with very similar functionality could be created. Make sure to allocate an appropriate amount of data memory as well as configure any necessary indices, e.g., Y. The amount of memory that should be allocated for the input string is, more or less, arbitrary. (In general, this should be dependent on the application.) However, it would be wise to utilize some form of a symbolic constant, e.g., one defined by the `.equ` keyword, so that the amount of data allocated could be readily changed.6.3. Use a serial terminal on your computer to test your program. Verify that backspace functionality is correct for at least one of [1] the backspace character (BS, 0x08) or [2] the delete character (DEL, 0x7F); it is not expected that both of these specified characters actually be utilized on some connected keyboard.<a href="#_ftn5" name="_ftnref5"><sup>[5]</sup></a></td>

      </tr>

     </tbody>

    </table>character is found, decrement the Y index to<strong>7.</strong> <strong>USART, INTERRUPT-BASED RECEIVING</strong>

    <table width="733">

     <tbody>

      <tr>

       <td width="389">In this section of the lab, you will learn how to configure interrupt-based receiving within the USART system by creating an interrupt-driven echo program for the appropriate USART module.7.1. Create an assembly program, <strong>lab5_7.asm</strong>, that utilizes the receive complete (RxC) interrupt within the appropriate USART module to echo (i.e., transmit back) to your</td>

       <td width="344"><em>GREEN_PWM</em> LED within the main routine of your program. (See the relevant <em>µPAD </em>schematic, if necessary.) Use a serial terminal program to test your assembly program. Within the serial terminal, make sure that local echoing, or anything similar, is disabled. (See § A.3 within Appendix A of this document to learn how to disable local echoing within <em>PuTTY</em>.)</td>

      </tr>

     </tbody>

    </table>computer any character received. Additionally, to <strong>NOTE</strong>: Recall that interrupt service routines should generally be demonstrate that your serial interrupt is independent from as short as possible; thus, it would generally be <em>unwise</em> to call a the rest of your program, continually toggle the subroutine within an ISR.</td>

  </tr>

 </tbody>

</table>

character received. Utilize the IN_CHAR and OUT_CHAR subroutines whenever appropriate. Use a terminal program on your computer to test your assembly program.




<strong> </strong>

respectively. This sequence of characters originates from typewriter times, when starting a new line of text involved the two-step process of turning a carriage to the position denoting the beginning of a line and then turning the platen (wheel) to the following line.

In systems based on <em>Unix</em>® or <em>Linux</em>®, a newline character consists of only a line feed character.




<h2><u>PRE-LAB PROCEDURE SUMMARY</u></h2>

<ul>

 <li>Answer pre-lab exercises when applicable.</li>

 <li>Become introduced to the USART system within the <em>ATxmega128A1U</em> in § 1. 3) Learn how to transmit a character via the USART system in § 2.</li>

 <li>Measure USART character transmissions with an oscilloscope in § 3. Take an appropriate screenshot for each relevant measurement and annotate when appropriate.</li>

 <li>Learn to transmit a character string of arbitrary length via the USART system in § 4.</li>

 <li>Create a subroutine to receive a character via the USART system in § 5.</li>

 <li>Implement a subroutine to receive a character string of arbitrary length via the USART system in § 6.</li>

 <li>Create an interrupt-driven echo program in § 7.</li>

</ul>




<table width="733">

 <tbody>

  <tr>

   <td width="361"><strong><u>APPENDIXES</u></strong></td>

   <td width="372"></td>

  </tr>

  <tr>

   <td width="361"><strong>A.</strong></td>

   <td width="372"><strong>PUTTY </strong></td>

  </tr>

 </tbody>

</table>

<em>PuTTY</em> is a lightweight terminal program with many features and settings, but for the purposes of this course, we will only need to use its serial operating mode. To start using <em>PuTTY</em>, you will first need to download the program to your PC.

<ul>

 <li>To download <em>PuTTY </em>for 64-bit operating systems, click <a href="https://the.earth.li/%7Esgtatham/putty/latest/w64/putty.exe">here</a><a href="https://the.earth.li/%7Esgtatham/putty/latest/w64/putty.exe">;</a> for 32-bit operating systems, click <a href="https://the.earth.li/%7Esgtatham/putty/latest/w32/putty.exe">here</a><a href="https://the.earth.li/%7Esgtatham/putty/latest/w32/putty.exe">.</a> Once downloaded, run the executable.</li>

</ul>

Upon the program opening, the <em>PuTTY</em> configuration menu should be displayed, as shown in Figure 2. This configuration menu is used to select the operation(s) of the terminal application. There are a few things that must be changed before we can start communication between our computer and the microcontroller.

<strong>Figure 2:</strong> <em>PuTTY</em> Configuration Menu

<ul>

 <li>In the configuration menu of <em>PuTTY</em>, do the following:

  <ul>

   <li>Select the <em>Session</em> tab at the top left. Choose <em>Serial</em> as the connection type (on the right, next to <em>SSH</em>).</li>

   <li>Select the <em>Serial</em> sub-tab on the left of the <em>PuTTY </em>configuration menu (located at the bottom of the <em>Connection </em>tab list).</li>

   <li>Choose and enter the correct COM (communication) port that corresponds to the <em>µPAD</em>. To determine which COM port on your computer represents the <em>µPAD</em>,

    <ul>

     <li>Open the preinstalled <em>Device Manager</em> application in <em>Windows</em>, expanding the <em>Ports</em></li>

     <li>Disconnect your <em>µPAD</em> USB cable and make a note of the COM ports that are available. (It is possible that no ports may be shown.)</li>

     <li>Re-connect your <em>µPAD</em> USB cable and notice the COM port was added to the list. This is the COM port that you should use in <em>PuTTY</em>, e.g., <strong>COM1</strong>, <strong>COM2</strong>, etc.</li>

    </ul></li>

   <li>Enter the correct baud rate in the <em>Speed (baud)</em> textbox, select the correct number of data and stop bits, and also select the correct type of parity. Additionally, set <em>Flow Control</em> to <strong><em>None</em></strong>. (Make sure that the data bits, stop bits, and type of parity are all configured as they are in the USART system within the <em>ATxmega128A1U</em>.)</li>

   <li>Once everything is configured, you can save your configuration settings so that you do not have to change them every time. To do this, do the following:

    <ul>

     <li>Navigate back to the <em>Session</em> menu, and in the textbox located under <em>Saved Sessions</em>, type something such as <strong><em>3744 UART Config</em></strong>. This will be the name used for your configuration.</li>

     <li>Next, click the button to the right labeled <strong><em>Save</em></strong>. This will save your current configuration, so that you can access it for the next time you use <em>PuTTY</em>. (To load a saved configuration, you will need to first click on the appropriate configuration listed within <em>Saved Sessions </em>and then click the button to the right</li>

    </ul></li>

  </ul></li>

</ul>

labeled <strong><em>Load</em></strong>.)

Now that everything is configured, you can open the terminal window by clicking the <strong><em>Open</em></strong> button located at the bottom right of the window.

<strong>NOTES: </strong>

<ul>

 <li>Configure <em>PuTTY</em> and open the terminal window <strong>BEFORE</strong> you debug/run your program in <em>Atmel Studio</em>.</li>

 <li>It is possible that the COM channel will change if you have different USB devices connected to your PC, or if you connect any USB devices in a different order. If this occurs, just repeat items 2 and 3 above to determine the proper COM port for your microcontroller.</li>

</ul>

Additionally, there is a setting in <em>PuTTY</em> that causes characters typed to the terminal to be echoed, i.e., displayed to the terminal automatically. This can be mistaken as a properly-working echo program, when in fact <em>PuTTY</em> might be the only source of echoing.

A.3. When applicable, to turn off the automatic echo setting, do the following:

<ul>

 <li>Open the configuration menu of</li>

 <li>Select the <em>Terminal </em></li>

 <li>Under <em>Local echo</em>, select <strong><em>Force off</em></strong>.</li>

</ul>

You can find more detailed information on <em>PuTTY’s</em> website, or by clicking on the following link: <a href="https://the.earth.li/%7Esgtatham/putty/0.70/htmldoc/"><em>PuTTY</em></a><a href="https://the.earth.li/%7Esgtatham/putty/0.70/htmldoc/"> User Manual</a><a href="https://the.earth.li/%7Esgtatham/putty/0.70/htmldoc/">.</a> <strong>        </strong>

<table width="735">

 <tbody>

  <tr>

   <td width="337"><strong>B.</strong><strong>                                                                                                           </strong><strong>BAUD RATE VS. HZ VS. BITS/SECOND </strong></td>

  </tr>

 </tbody>

</table>

In general, a <strong>baud</strong> <strong>rate </strong>represents the rate of <strong>symbols per second</strong>, where a <strong>symbol</strong> represents a relevant unit. In this course, the relevant symbol for a baud rate is a <em>bit</em>.  Therefore, the overall unit of any baud rate in this course is <strong>bits/second </strong>(<strong>bps</strong>).

Additionally, since one bit of data is transmitted per one cycle of a respective clock signal, and since the unit of hertz (Hz) represents <em>cycles per second</em>, we can state that a baud rate of 1 bps implies that the respective clock signal has a frequency of 1 Hz.

To learn more about communication theory, take <em>EEL4514: </em>

<table width="735">

 <tbody>

  <tr>

   <td width="241"><strong>C.</strong><strong>                                                                                                                                 </strong><strong>DEBUGGING WITH UART</strong></td>

  </tr>

 </tbody>

</table>

<em>Communication Systems and Component</em>




The <em>ATxmega128A1U</em> UART module <strong>does not</strong> continue to run while the processor is halted at a breakpoint. Consequently, if you are trying to debug your code by setting breakpoints anywhere near your UART functions, or code that calls your UART functions, your character transmissions are very likely to become <strong>corrupted</strong>. If you use breakpoints, you should be aware of this fact.

Fortunately, you have many tools at your disposal. Halting the program to view IO memory and registers can still be useful, as long as you are aware that stepping through the code line by line will interfere with UART communication. Additional debugging techniques include outputting useful information (such as the USART STATUS register) via LEDs at runtime. Another common debugging technique is printing information to the terminal.

<a href="#_ftnref1" name="_ftn1">[1]</a> You have previously utilized parallel communication whenever using an I/O port or the external bus interface (EBI) system.

<a href="#_ftnref2" name="_ftn2">[2]</a> To learn more about baud rates in the context of this course, see Appendix B of this document.

<a href="#_ftnref3" name="_ftn3">[3]</a> In addition to providing the functionality of a UART, the USART system also provides a form of synchronous serial communication to the <em>ATxmega128A1U</em>. <sup>4</sup> By “arbitrary length”, we mean any length of allottable, contiguous data memory locations.

<a href="#_ftnref4" name="_ftn4">[4]</a> To denote when text should start to appear at the beginning of a following line, a special sequence of values, comprising what is known as a <em>newline</em>, is utilized. Usually, a newline is generated by an Enter key or Return key on a computer keyboard, and, with most operating systems, a newline consists of at least a <em>line feed </em>(LF, 0x0A, 
) character. However, with <em>PuTTY </em>and some other serial terminal programs, only a <em>carriage return</em> (CR, 0x0D, r) character is used to represent a newline, at least by default.

In the <em>Windows</em>® operating system, a newline is a carriage return character, followed by a line feed character, to designate that a relevant cursor should both return to the beginning of a line of text and advance to the next line of text,

<a href="#_ftnref5" name="_ftn5">[5]</a> The delete character code is included here since serial terminal programs (e.g., <em>PuTTY</em>) utilize, by default, the delete character code to represent a “backspace”.