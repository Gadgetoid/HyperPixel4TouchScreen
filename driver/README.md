# HyperPixel4 Goodix Touchscreen Driver

## Installing

### Users

Install the alternate touch driver from the .deb file available in releases:

```
wget https://github.com/pimoroni/HyperPixel4TouchScreen/releases/download/v1.0/hyperpixel4-goodix-dkms_1.0_all.deb
sudo apt install dkms raspberrypi-kernel-headers
sudo dpkg -i hyperpixel4-goodix-dkms_1.0_all.deb
```

Then install the modified dtoverlay from this repository:

```
git clone https://github.com/pimoroni/HyperPixel4TouchScreen
cd HyperPixel4TouchScreen/driver
make build
sudo make install
```

HyperPixel4's touchscreen can be rotated with the following options in `/boot/config.txt`:

* `dtoverlay=hyperpixel4:rotate_0` - Portrait, USB ports on top
* `dtoverlay=hyperpixel4:rotate_1` - Landscape, USB ports on left
* `dtoverlay=hyperpixel4:rotate_2` - Portrait, USB ports underneath
* `dtoverlay=hyperpixel4:rotate_3` - Landscape, USB ports on right

These all match the number you supply to `display_lcd_rotate=`.

Note: both Portrait orientations must have `framebuffer_width=480` and `framebuffer_height=800` specified instead of the defaults in `/boot/config.txt`.

### Developers

To build and install the kernel module, you need dkms:

```
sudo apt install raspberrypi-kernel-headers dkms
cd driver
sudo ln -s /full/path/to/goodix-1.0 /usr/src/goodix-1.0
sudo dkms install goodix/1.0
```

Added firmware writing of:

```touchscreen-size-x```
	Sets the maximum x size (narrow edge)
	
```touchscreen-size-y```
	Sets the maximum Y size (long edge)
	
```touchscreen-x2y```
	If included sets x2y flag on, otherwise x2y flag is off.
	
```touchscreen-refresh-rate```
	Sets the refresh rate, range 0-15ms. This is added to the 5ms overhead of the screen giving a range 5-20ms.
	Default is 5 giving 10ms refresh rate.
	Setting this higher drastically reduces cpu load but increases lag, setting lower decreases lag and increases cpu load.
	
Example with touch screen offering 16 times resolution of lcd

```
touchscreen-size-x = <7680>;
touchscreen-size-y = <12800>;
```





boot/config and hyperpixel4.dts fragments for different orientations:

```
framebuffer_width=480
framebuffer_height=800
display_rotate=0
```


    fragment@3 {
        target = <&i2c_gpio>;
        __overlay__ {
            /* needed to avoid dtc warning */
            #address-cells = <1>;
            #size-cells = <0>;
            ft6236: ft6236@5d {
                compatible = "goodix,gt911";
                reg = <0x5d>;
                interrupt-parent = <&gpio>;
                interrupts = <27 2>;
                touchscreen-inverted-x;
	    };
        };
    };


```
framebuffer_width=800
framebuffer_height=480
display_rotate=1
```

    fragment@3 {
        target = <&i2c_gpio>;
        __overlay__ {
            /* needed to avoid dtc warning */
            #address-cells = <1>;
            #size-cells = <0>;
            ft6236: ft6236@5d {
                compatible = "goodix,gt911";
                reg = <0x5d>;
                interrupt-parent = <&gpio>;
                interrupts = <27 2>;
                touchscreen-swapped-x-y;
	    };
        };
    };


```
framebuffer_width=480
framebuffer_height=800
display_rotate=2
```

    fragment@3 {
        target = <&i2c_gpio>;
        __overlay__ {
            /* needed to avoid dtc warning */
            #address-cells = <1>;
            #size-cells = <0>;
            ft6236: ft6236@5d {
                compatible = "goodix,gt911";
                reg = <0x5d>;
                interrupt-parent = <&gpio>;
                interrupts = <27 2>;
                touchscreen-inverted-y;
	    };
        };
    };


```
framebuffer_width=800
framebuffer_height=480
display_rotate=3
```

    fragment@3 {
        target = <&i2c_gpio>;
        __overlay__ {
            /* needed to avoid dtc warning */
            #address-cells = <1>;
            #size-cells = <0>;
            ft6236: ft6236@5d {
                compatible = "goodix,gt911";
                reg = <0x5d>;
                interrupt-parent = <&gpio>;
                interrupts = <27 2>;
                touchscreen-inverted-x;
                touchscreen-inverted-y;
                touchscreen-swapped-x-y;
	    };
        };
    };


