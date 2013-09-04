I am using [this Python test script](https://github.com/osudrl/atrias/blob/374b7dde6a13065d6b9810f221f51f5b39029d9b/software/atrias/scripts/kvh_ahrs_test.py) to troubleshoot the KVH 1750 IMU in six different modes:

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
     than in the other (staying within gyro bandwidth).
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

For 1 and 2, I scoped the TOV output and found they were pulsing at 962.4 Hz
instead of the expected 1000 Hz. Note that 1000/962.4 = 180/173 = 1.039.

The following do not affect the results above:

  * Changing data rate all the way down to 1 Hz
  * Lowering baudrate down to 115200 baud
  * Using the Y and Z axes

In addition, the sequence bytes look good. The status byte is always `0x77`, as
shown in the log files. BIT data is `7f7f7f7f7f7f`, as it should be.

However, extended BIT fails and returns the following:

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

This leaves me with two questions:

  1. Why is there a scale factor error of 1.039 for both rate and delta angle
     modes when MSync is internal?
  2. Why do the numbers make no sense in delta angle mode when MSync is
     external?

<!--
vim: ft=markdown
-->

