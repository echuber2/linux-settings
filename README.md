# linux-settings
Various settings for Linux stuff

## Fixing screen tearing in Cinnamon

### Screen tearing fixes on intel

#### Using uxa/glamor instead of sna
If you have a laptop with Nvidia dGPU/PRIME, it's complicated to ensure there is no tearing. The simplest solution for me is to leave the dGPU off most of the time, using the Intel iGPU and adjusting the settings for the `intel` driver (aka `xf86-video-intel`, `xserver-xorg-video-intel`).

The file `20-intel-smooth.conf` contains a settings change I found necessary to eliminate tearing. In particular, I need to use `uxa` or `glamor`, not `sna` for the the acceleration mode. (If you haven't specified any mode, I believe `sna` is the default.)

You can integrate the settings wherever you are currently sourcing your `xorg.conf` files. If you don't know where to put it, kill your X server and run this command from a tty:

`sudo X -configure`

If it doesn't complain about X already running, it will generate a starter xorg.conf and tell you where to put it.

#### Clutter hacks for Cinnamon
If you're using Cinnamon/clutter, another option that may give better framerates for full-screen 3D is to use `sna` mode but add these lines to `/etc/environment`:

`CLUTTER_PAINT=disable-clipped-redraws:disable-culling`   
`CLUTTER_VBLANK=True`

I found this works sometimes but there is a full-screen diagonal tear on some applications as the window rectangle is synced as two separate triangles out of sync by a frame, apparently. The issue may be resolved on newer driver builds. With `uxa`/`glamor` modes these clutter hacks seem to make occasional stuttering get worse.

#### modesetting driver
Many people reported that uninstalling the intel driver and using the `modesetting` driver instead solved their issues. However when I try to use `modesetting`, Cinnamon always falls back to software rendering mode. :-/

### Screen tearing fixes on nvidia
The issue with PRIME mixed-gpu laptops is complicated and only got support recently (as of late 2016/early 2017). If you want to stop tearing when the dGPU is running, you can follow the latest instructions from Nvidia [here](https://devtalk.nvidia.com/default/topic/957814/linux/prime-and-prime-synchronization/).

On a desktop dGPU setup, there may be an easier solution. [link](https://www.gamingonlinux.com/articles/i-have-finally-found-a-way-to-sort-out-screen-tearing-on-nvidia-with-linux.7213/)
