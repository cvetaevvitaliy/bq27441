# bq27441
This library interfaces with the Texas Instruments BQ27441 Battery Fuel Gauge using i2c
---------------------

Usage
-----

Copy sources, add header file to your project
```c
#include "bq27441.h"
```

The library does not depend on the i2c implementation, you need to implement this yourself and register read/write callbacks before initializing bq27441 chip

You can use any microcontrollers or even Linux user-space to read data from this sensor, it's all up to you <br>
For example, use STM32 and HAL device library <br>

i2c read/write
```c
static int16_t BQ27441_i2cWriteBytes(uint8_t DevAddress, uint8_t subAddress, uint8_t * src, uint8_t count)
{
    if (HAL_I2C_Mem_Write(&hi2c1, (uint16_t)(DevAddress << 1), subAddress, 1, src, count, 50) == HAL_OK)
        return true;
    else
        return false;
}

static int16_t BQ27441_i2cReadBytes(uint8_t DevAddress, uint8_t subAddress, uint8_t * dest, uint8_t count)
{
    if (HAL_I2C_Mem_Read(&hi2c1, (uint16_t)(DevAddress << 1), subAddress, 1, dest, count, 50) == HAL_OK)
        return true;
    else
        return false;
}
```

.... <br>

Somewhere in your services init function or hardware perif init
```c
    BQ27441_ctx_t BQ27441 = {
            .BQ27441_i2c_address = BQ72441_I2C_ADDRESS, // i2c device address, if you have another address change this
            .read_reg = BQ27441_i2cReadBytes,           // i2c read callback 
            .write_reg = BQ27441_i2cWriteBytes,         // i2c write callback 
    };
    
/**
 * Your other code
 * */

BQ27441_init(&BQ27441);

```

After init chip and register read/write functions, you can read the battery readings, all function descriptions are in source files

For example read voltage, power, current, capacity
```c
/**
 * Reads and returns the battery voltage
 *
 * @return battery voltage in mV
 * */
uint16_t BQ27441_voltage (void);

/**
 * Reads and returns the specified current measurement
 * @param current_measure enum specifying current value to be read
 * @return specified current measurement in mA. >0 indicates charging.
 * */
int16_t BQ27441_current (current_measure type);

/**
 * Reads and returns the specified capacity measurement
 *
 * @param capacity_measure enum specifying capacity value to be read
 * @return specified capacity measurement in mAh.
 * */
uint16_t BQ27441_capacity (capacity_measure type);

/**
 * Reads and returns measured average power
 *
 * @return average power in mAh. >0 indicates charging.
 * */
int16_t BQ27441_power (void);
```

References
---------------------

+ Texas Instruments [BQ27441 product page][1].
+ BQ27441 [Datasheet][2].
+ BQ27441 [User's Guide][4].
+ BQ27441 [Technical Reference][3].

[1]:http://www.ti.com/product/bq27441-g1
[2]:http://www.ti.com/lit/gpn/bq27441-g1
[3]:http://www.ti.com/lit/pdf/sluuac9
[4]:http://www.ti.com/lit/pdf/sluuap4
