
# Setting up ices2

```
apt install ices2
```

Copy `ices-alsa.xml` to `/etc/ices2/`. Create the directory if necessary. Set permissions so only the user that will be running `ices2` can read and write the config file. Change the config file setting the correct password.

Find the alsa ID of the recording device you're using:

```
arecord -l
```

You will get some output like:

```
**** List of CAPTURE Hardware Devices ****
card 1: H3VR [H3-VR], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

In this example we have `card 1` with `subdevice #0`. The alsa device specifier to use in the config file would then be `hw:1,0`. So the section would look like:

```
<input>
  <module>alsa</module>
  <param name="rate">48000</param>
  <param name="channels">2</param>
  <param name="device">hw:1,0</param>
</input>

```

You should also change the rate in the `<input>` section and samplerate in the `<encode>` section to the actual rates required if that is not 48000. You can comment out and modify the `<resample>` section if the input and output sample rates aren't the same.

# Testing

```
ices2 /etc/ices2/ices-alsa.xml
```

Check the log file `/var/log/ices.log` for any errors and check the icecast server to see if the stream works.

# Make ices2 start automatically on boot

```
cp ices2.initd /etc/init.d/ices2
chown 755 /etc/init.d/ices2
```

Edit the `ices2` script to change which user and group to run as. This user should have read access to the config file, write access to the log file and access to the alsa audio device.


```
cp ices2.service /etc/systemd/system/ices2.service
systemctl enable ices2.service
```

Try to start and stop using :

```
service ices2 start
service ices2 stop
```