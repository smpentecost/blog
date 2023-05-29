---
draft: true
tags: []
author: Seeley
showToc: true
TocOpen: false
hidemeta: false
comments: false
disableHLJS: true
disableShare: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
  image: <image path/url>
  alt: <alt text>
  caption: <text>
  relative: false
  hidden: true
title: Building a JTAGulator
description: ""
date: 2020-08-21T22:09:00-04:00
type: posts
---

The [JTAGulator](http://www.grandideastudio.com/jtagulator/), from Grand Idea Studio is an open source tool for quickly identifying on chip debug (OCD) interfaces provided on many consumer electronics. OCD interfaces are a popular attack avenue for hardware hackers as they are often unsecured, and provide direct access to a device's memory content. You can purchase premade JTAGulator's from several different websites usually for ~$170. However, being an open source project, Grand Idea Studio provides all of the information you need to build a JTAGulator at home. Naturally, I had to give it a shot.


## Getting Materials {#getting-materials}

The documentation section on the [JTAGulator webpage](http://www.grandideastudio.com/jtagulator/) provides a block diagram, full schematic, bill of materials (BOM), assembly drawing and test procedure document. Assuming you have a suitable soldering station for surface mount components, getting the components you need for your JTAGulator is as easy as uploading the BOM (reproduced in table format [here](/misc/jtagulator-bom) for your convenience) to DigiKey's order page and putting in your information. The price should come out to ~$50.

You will also either need to get a board made using a provider like [OSHPark](https://oshpark.com/) or [JLCPCB](https://jlcpcb.com/) or source a board from somewhere else. For my build, I actually found a listing on Ebay selling the boards for $10 with free shipping. I assume that another JTAGulator-curious individual ordered many boards from a manufacturer and is now slowly selling them to make up the cost of their JTAGulator build. If you have friends that are also interested in building a JTAGulator, consider buying your boards and components as a group to take advantage of bulk prices and consolidated shipping. When all is said and done, you should be able to get everything you need for ~$60.


## Building {#building}

If you're not very comfortable with surface mount soldering, the JTAGulator is the perfect project to get your hands dirty. The documentation is clear and the parts are well laid out with plenty of room between devices. A homebrew basement workstation will do just fine. However, if you're still apprehensive about diving in, there are a few things you can do to smooth out the process:

{{< figure src="/ox-hugo/workbench.jpg" caption="Figure 1: My basement workstation." width="600px" >}}

1.  Label your part bags with the index numbers from the BOM. Surface mount components are small and easy to lose on even the most organized of workbenches. By labeling the part bags ahead of time, you can more quickly and easily identify fit and placement for components without having to check part numbers/descriptions. Labeling your bags is also a good time to do an inventory and make sure that your part supplier actually shipped you everything they were supposed to.

2.  Buy some new soldering iron tips. There is a common misconception that working with tiny devices means you need a tiny soldering iron tip. However, if you're using the common soldering technique called [drag soldering](https://www.hakko.com/english/tip%5Fselection/work%5Fdrag.html), the size of the tip isn't super important. The important thing is having a clean well-tinned tip that quickly and evenly heats contacted pins. So ditch your 6 year old, oxidized to all hell tip that came with your iron, and drop $10 on a set of new tips from Amazon.

3.  Buy some precision tweezers. As with most DIY projects, having the right tool can make all the difference. Precision tweezer kits can be had for <$10 on Amazon and will greatly improve your chance of success when positioning surface mount components.

4.  Print out the documentation. Maybe you like the markup tools in your favorite pdf viewer, but you will probably get more mileage out of a few sheets of paper and a pencil. By printing all of the documentation, you can easily check items off as you go, make notes about successful/failed continuity tests, etc.

5.  Finally, make sure that your workstation is well lit, and preferably equipped with a good magnifying glass. Inspecting the quality of your solder joints is far easier when you can actually see them.

I chose to wait on the header pins and terminal blocks until after testing, so that they wouldn't be in the way if I needed to resolder anything (which wound up being a good decision).

{{< figure src="/ox-hugo/test_ready2.png" caption="Figure 2: My test ready JTAGulator. Please forgive the gobs of flux." width="600px" >}}


## Testing {#testing}


### Continuity Testing {#continuity-testing}

After completing your solder job, the next thing you're going to want to do is check continuity. At the outset, this can seem tedious but it can significantly reduce troubleshooting time down the road. Simply start on one end of the schematic and work your way across checking that each connection has been made.

A note about continuity checking surface mount components. It can be tempting to position the probe on the pad, however this only checks continuity between traces (which shouldn't be an issue if a your board manufacturer was competent). Be sure to position your probes with direct contact to device pins, as shown below.

{{< figure src="/ox-hugo/probe_guide.jpg" caption="Figure 3: Continuity probe positioning." width="600px" >}}


### Hardware Testing {#hardware-testing}

Once you have completed continuity testing, you should follow the test procedures outlined in the "Test Procedure" document on Grand Idea Studio's webpage. If continuity testing was comprehensive and successful, the official test procedures will confirm that you don't have any malfunctioning components.

Part of the official test procedure is programming your EEPROM with the latest JTAGulator code (I chose to use v1.6 in case of stability issues with v1.7). There are several software options for programming the chip but I would encourage you to use the [Parallax Propeller Tool](https://www.parallax.com/downloads/propeller-tool-software-windows-spin-assembly). I use Linux as my daily driver so the fact that the Propeller Tool is only for Windows and Mac is annoying. However, PropellerIDE and SimpleIDE, simply [do not work](https://github.com/grandideastudio/jtagulator/issues/20). Additionally, if Joe Grand says Propeller Tool is the best option, I'm inclined to believe him.


### Software Testing {#software-testing}

The final (and most fun test) is of the software. I wanted to check UART and JTAGulation functionality on a well documented device and a spare Raspberry Pi 3B laying on my desk seemed like the ideal option. Obviously, by knowing the pins ahead of time, this allows me to confirm that the JTAGulator is able to correctly identify the interface.


#### UART Testing {#uart-testing}

1.  The first step in setting up your test is enabling serial on your RPi. I find that the easiest way to enable the serial pins is through the [raspi-config](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-5-using-a-console-cable/enabling-serial-console) utility built into Raspbian.
2.  Connect up your RPi and JTAGulator according to the table:

<style>.table-nocaption table { text-align: center;  width: auto;  display: table;  margin-right: auto;  margin-left: auto;  }</style>

<div class="ox-hugo-table table-nocaption">
<div></div>

| Function | RPi Pin | JTAGulator |
|----------|---------|------------|
| GROUND   | 6       | GND        |
| TXD      | 8       | CH0        |
| RXD      | 10      | CH1        |

</div>

<ol class="org-ol">
<li value="3">Next, plug your JTAGulator into your computer and power on the RPi.</li>
<li>Open up your favorite serial terminal and connect to the JTAGulator.</li>
<li>Your first command should always be setting the voltage of the JTAGulator to what your device under test can handle. In this case, the voltage should be set to 3.3V.</li>
<li>Finally, start a UART discovery test. If everything is in working order, the JTAGulator should identify the UART port at a baud rate of 115200Hz.</li>
</ol>

{{< figure src="/ox-hugo/UARTScanTerminal.jpg" caption="Figure 4: A successful UART scan on a RPi 3B." width="500px" >}}

I also had my logic analyzer sitting on my desk so I hooked it up during the test with the JTAGulator on D3 and the RPi on D1. Notice that after the 17th interrogation, the RPi responds, indicating that the selected baud rate (115200 in this case) is supported by the interface.

{{< figure src="/ox-hugo/UARTScanPulseView.jpg" caption="Figure 5: A logic capture of a successful UART scan on a RPi 3B." width="800px" >}}

Putting the JTAGulator in UART passthrough mode will further verify the results of the scan. The JTAGulator is smart enough to auto populate your pin arguments based on the results of the scan. A login prompt confirms that the scan results were correct.

{{< figure src="/ox-hugo/UARTPassthroughTerminalLogin.jpg" caption="Figure 6: UART passthrough mode with a RPi 3B" width="500px" >}}


#### JTAG Testing {#jtag-testing}

1.  Enable the JTAG pins on the RPi. The easiest way to do this is by adding `enable_jtag_gpio=1` to your config.txt file in the root of the boot directory on your Raspbian SD card.
2.  Connect up your RPi and JTAGulator according to the table:

<style>.table-nocaption table { text-align: center;  width: auto;  display: table;  margin-right: auto;  margin-left: auto;  }</style>

<div class="ox-hugo-table table-nocaption">
<div></div>

| Function | RPi Pin | JTAGulator |
|----------|---------|------------|
| GROUND   | 6       | GND        |
| TMS      | 13      | CH0        |
| TRST     | 15      | CH1        |
| TDO      | 18      | CH2        |
| TCK      | 22      | CH3        |
| TDI      | 37      | CH4        |

</div>

{{< figure src="/ox-hugo/test_setup.jpg" caption="Figure 7: RPi 3 and JTAGulator setup for the JTAG test." width="500px" >}}

<ol class="org-ol">
<li value="3">Be sure to set your voltage to 3.3V.</li>
<li>Start scanning.</li>
</ol>

The IDCODE scan does what it sounds like, it looks for the device ID by finding TDO, TMS and TCK.

{{< figure src="/ox-hugo/IDCODEScanTerminal.jpg" caption="Figure 8: IDCODE scan of a RPi 3B." width="500px" >}}

Once we have this information, the JTAGulator will auto populate a more in-depth scan, called a BYPASS scan. This will identify TDI, now giving us enough information to connect a JTAG debugger and OpenOCD.

{{< figure src="/ox-hugo/BYPASSScanTerminal.jpg" caption="Figure 9: BYPASS scan of a RPi 3B." width="500px" >}}


## Wrap Up {#wrap-up}

I really enjoyed building the JTAGulator and I am looking forward to using it on some other projects in the near future. I hope this article helped answer any questions you may have had about the build process and encourages you to build your own. Thanks for reading.
