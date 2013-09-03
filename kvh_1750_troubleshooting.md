I am using [this Python script](https://github.com/osudrl/atrias/blob/374b7dde6a13065d6b9810f221f51f5b39029d9b/software/atrias/scripts/kvh_ahrs_test.py) to troubleshoot the KVH 1750 IMU in four different modes:

  1. Direct connection to IMU, 1000 Hz, internal MSync, delta angle
  2. Direct connection to IMU, 1000 Hz, internal MSync, rate
  3. Serial passthrough with embedded device, 500 Hz, external MSync, delta angle
  4. Serial passthrough with embedded device, 500 Hz, external MSync, rate

Otherwise, I am using factory default settings. In 3 and 4, DR is 1000, though I'm toggling the msync pin at 500 Hz (verified with oscilloscope).
 
scale factor error 1.039 in 1 and 2 (using marks on the tabletop to rotate the IMU 180 degrees)
nonsensically large numbers in 3
no scale factor error in 4

IMU internal timer is off maybe?
changing data rate does not help.
lowering baudrate (and the data rate, appropriately) does not help.
Y and Z axes seem to behave similarly.
tried spinning fast in one direction, slowly in the other. Zeroes properly. (Of course stayed within gyro bandwidth.)
Sequence bytes look good.
Status byte is 0x77, as it should be.
BIT data is 7f7f7f7f7f7f, as it should be.
Extended BIT fails:

	FPGA data output passed
	FPGA data acquisition passed
	Accel A passed
	Accel B passed
	Accel C passed
	ADC passed
	Aux SPORT failed!
	High speed SPORT passed
	GCB passed
	SELFTEST failed!


	CFG>INVALID,=DOFMT,0,EO0,1,@

	CFG>INVALID,=DOFMT,1,EO1,1,@

	CFG>INVALID,=DOFMT,2,EO2,1,@

	CFG>INVALID,=PROMPTS,1

	CFG>


not a buffer overflow issue because checking serial line for new packets at 1250 Hz

<!--
vim: ft=markdown
-->

