# Kwikflix UDP send
## sendts

### Features

* Sending ts file(s) as a ts udp stream

* If there is no files to send, it sends null packets

* Works with real-time process/threads priorities to provide stability of the stream

* Works with FIFO files

* Reading files to a cache buffer with an accumulation part to provide stability of the stream

### Tested environments

* Debian Jessy

* Ubuntu 14.04 LTS with lowlatency kernel (`sudo apt-get install linux-lowlatency` and restart system). It provides really stable TS stream.

### Build

Just run make in the project directory

### Command-line options

* -i &lt;address&gt; - ip address to send packets

* -p &lt;port&gt; - port to send packets

* -b &lt;bitrate&gt; - bitrate of stream

* -d &lt;path to directory&gt; - send all files from a directory, sorted my modified time, the directory is scanned in real-time, you can add files on fly

* -f &lt;path to file&gt; - send one file, you have to select either a file or a directory

* --ts_in_cache &lt;number&gt;, -s &lt;number&gt; - common cache size, you can use K to set the size in kilobytes and M in megabytes

* --accumul_ts &lt;number&gt;, -a &lt;number&gt; - size of filled cache part, you can use K to set the size in kilobytes and M in megabytes

* --ttl &lt;number&gt;, -t &lt;number&gt; - set ttl

* --pri &lt;number&gt;, -P &lt;number&gt; - set the process/thread priority

* --ts_in_udp &lt;number&gt;, -u &lt;number&gt; - the number of ts packets in one udp packet

* -c - don't stop file reading if it read zero bytes, use it for FIFO files

* -m - print debugging messages on the screen

* -l &lt;file name&gt; - save debugging messages to a file 

* -D &lt;seconds&gt; - print the state of buffer in some delays (in seconds)

* -F &lt;file size&gt; - set a minimal file size that we're waiting for, you can use K for kilobytes and M for megabytes 

* -M - set a minimal file size, based on accumulation buffer size (accumuluation size + 25% of it) 

* -v[0-3] - verbosity level

###Verbosity

* -v0 - prints nothing

* -v1 - prints minimal information about processing

* -v2 - prints information about buffer state with one second delay

* -v3 - prints debugging messages 

###Examples

####Read files from a directory
```
sudo /opt/sendts/bin/sendts -d /ts -i 239.0.0.10 -p 1234 -l ./log --ts_in_cache 40000 --accumul_ts 20000 -b 2000320
```

####Read and stream file, generated by ffmpeg
```
sudo /opt/sendts/bin/sendts -f /ts/channel1.ts -i 239.0.0.10 -p 1234 -l ./log --ts_in_cache 40000 --accumul_ts 20000 -b 2000320 -c -F 9520000
```
Above command will read /ts/channel1.ts file and send it as UDP stream with constant bitrate of 2000320.

####Generating proper mpegts stream with ffmpeg v2.8
```
ffmpeg -nostdin -i rtmp://XXX.XXX.XXX.XXX/pool/channel1 -xerror -err_detect explode -copytb 0 -vsync 0 -c copy -mpegts_original_network_id 0x1122 -mpegts_transport_stream_id 0x3344 -mpegts_service_id 0x5569 -mpegts_pmt_start_pid 0x1500 -mpegts_start_pid 0x150 -metadata service_provider="My Company" -metadata service_name="Channel1" -muxrate 2000320 -bsf:v h264_mp4toannexb -f mpegts -y /ts/channel1.ts
```
/ts/channel1.ts can be simple file or fifo (IMPORTANT NOTE: We are not recommending using fifo until we found solution to prevent sending garbage data)

### Feedback and tickets

We will appreciate you feedback or any contribution to our app. Please create a ticket in case of any question.

### Contribution

Code must be formatted with Artistic Style Utility: http://astyle.sourceforge.net/

Astyle is command-line utility and you have to use such options to format:
./astyle sendts.c -p -c -xC110 -xe -k3 -A14

We reject contributions that aren't being formatted with astyle.
