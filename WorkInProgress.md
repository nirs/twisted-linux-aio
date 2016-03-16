# It is still work in progress! #

Linux async I/O implementation is still work-in-progress and there's no perfect solution.

There are other solutions:

  * check out [Polyakov's work](http://lwn.net/Articles/172844/) (not developed anymore)
  * check out [POSIX aio for Linux](http://www.bullopensource.org/posix/)
  * check out [eventfd](http://lwn.net/Articles/225714/)
  * check out [Suparna's patches](http://www.kernel.org/pub/linux/kernel/people/suparna/aio/)

Also, buffered AIO is still to be done! That's right! Everything you read on Linux, using O\_DIRECT is _unbuffered_.

Fortunatley, it seems to be possible to use eventfd with AIO, [see here](http://lwn.net/Articles/226252/). C example included in our trunk.

# It still can block! #

io\_submit - the procedure called by Queue.schedule functions (Queue.scheduleRead, for example) can be blocking.

When?

  * file is not opened with os.O\_DIRECT flag
  * output data buffer is not page-aligned
  * you run out of block layer requests:
```
You don't mention what hardware you are running this on (the disk sub
system). io_submit() will block, if you run out of block layer requests.
We have 128 of those by default, but if your io ends up getting chopped
into somewhat smaller bits than 1MiB each, then you end up having to
block on allocation of those. So lets say your /src is mounted on
/dev/sdaX, try:

# echo 512 > /sys/block/sda/queue/nr_requests
```
> > -- found [here](http://linux.derkeiler.com/Mailing-Lists/Kernel/2006-11/msg00966.html).
  * also this:
```


On Linux, there is a system-wide limit of the maximum number of parallel KAIO requests. The /proc/sys/fs/aio-max-nr file contains this value. The Linux system administrator can increase the value, for example, by using this command:

# echo new_value > /proc/sys/fs/aio-max-nr
```