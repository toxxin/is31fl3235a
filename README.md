# is31fl3235a
A rust-embedded driver for the Lumissil Microsystems IS31FL3235a LED driver

## Description

IS31FL3235(A) is comprised of 28 constant current
channels each with independent PWM control,
designed for driving LEDs. The output current of each
channel can be set at up to 38mA (Max.) by an external
resistor and independently scaled by a factor of 1, 1/2,
1/3 and 1/4. The average LED current of each channel
can be changed in 256 steps by changing the PWM
duty cycle through an I2C interface.

## Example (based on stm32l432kc)

```rust
let mut rcc = cx.device.RCC.constrain();
let mut pwr = cx.device.PWR.constrain(&mut rcc.apb1r1);

let clocks = rcc
    .cfgr
    .sysclk(80.MHz())
    .pclk1(80.MHz())
    .pclk2(80.MHz())
    .freeze(&mut flash.acr, &mut pwr);

let mut scl = gpiob.pb6.into_alternate_open_drain(&mut gpiob.moder, &mut gpiob.otyper, &mut gpiob.afrl);
let mut sda = gpiob.pb7.into_alternate_open_drain(&mut gpiob.moder, &mut gpiob.otyper, &mut gpiob.afrl);

scl.internal_pull_up(&mut gpiob.pupdr, true);
sda.internal_pull_up(&mut gpiob.pupdr, true);

let mut i2c1 = I2c::i2c1(cx.device.I2C1, (scl, sda), i2c::Config::new(100.kHz(), clocks), &mut rcc.apb1r1);

let mut led_driver = is31fl3235a::Is31fl3235a::new(i2c1, LED_DRIVER_I2C_ADDR).unwrap();

led_driver.power_on().unwrap();

led_drv.set_pwm(1, 0xff).unwrap();
```
