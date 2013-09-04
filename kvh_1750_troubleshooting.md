I am using [this Python test script](https://github.com/osudrl/atrias/blob/374b7dde6a13065d6b9810f221f51f5b39029d9b/software/atrias/scripts/kvh_ahrs_test.py) to troubleshoot the KVH 1750 IMU in four different modes:

  1. Direct connection to IMU, 1000 Hz, internal MSync, delta angle
  2. Direct connection to IMU, 1000 Hz, internal MSync, rate
  3. Direct connection to IMU, 1000 Hz, external MSync, delta angle
  4. Direct connection to IMU, 1000 Hz, external MSync, rate
  5. Serial passthrough with embedded device, 500 Hz, external MSync, delta angle
  6. Serial passthrough with embedded device, 500 Hz, external MSync, rate

Otherwise, I am using factory default settings. In 5 and 6, DR is 1000, though
I'm toggling the msync pin at 500 Hz (verified with oscilloscope).

The IMU is located on a flat surface on which I've marked the outline of the
IMU's base. In all six modes, I:

  1. Rotate IMU 180 degrees about X axis
  2. Hold for a few seconds
  3. Rotate IMU -180 degrees about X axis
  4. Hold for a few seconds
  5. Rotate IMU back and forth about X axis, rotating faster in one direction
     than in the other.
  6. Rotate IMU back to beginning position.

For each of the six modes, I can observe the following:

  1. Logfile: [kvh\_direct\_imu\_delta.log](kvh_direct_imu_delta.log)
    * After step 1: the test script shows a rotation of about 173 degrees
      instead of the expected 180.
    * After step 3, the script indicates 0 degrees, as expected.
    * After step 6: the script indicates 0 degrees, as expected.
  2. Logfile: [kvh\_direct\_imu\_rate.log](kvh_direct_imu_rate.log)
    * Same result as 1.
  3. Logfile: [kvh\_direct\_ext\_delta.log](kvh_direct_ext_delta.log)
    * Unreasonably large numbers. Angle estimate never returned to zero.
  4. Logfile: [kvh\_direct\_ext\_rate.log](kvh_direct_ext_rate.log)
    * Numbers make sense.
  5. Logfile: [kvh\_medulla\_ext\_delta.log](kvh_medulla_ext_delta.log)
    * Unreasonably large numbers. Angle estimate never returned to zero.
  6. Logfile: [kvh\_medulla\_ext\_rate.log](kvh_medulla_ext_rate.log)
    * Numbers make sense.

scale factor error 1.039 in 1 and 2 (using marks on the tabletop to rotate the IMU 180 degrees)
nonsensically large numbers in 3
no scale factor error in 4

Scoped TOV output -- 962.4 Hz instead of the expected 1000 Hz. Note that 1000/962.4 = 1.039.
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

