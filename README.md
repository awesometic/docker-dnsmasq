# docker-dnsmasq

dnsmasq in a docker container, configurable via a [simple web UI](https://github.com/jpillora/webproc)

[![Docker Pulls](https://img.shields.io/docker/pulls/awesometic/dnsmasq.svg)][dockerhub]
[![Image Size](https://images.microbadger.com/badges/image/awesometic/dnsmasq.svg)][dockerhub]

### Has preset for EFI systems

This image has a default dnsmasq.conf file for EFI systems that only works with when the other PXE server exists.

The PXE server working confirmed with is [linuxserver/netbootxyz](https://github.com/linuxserver/docker-netbootxyz).

If you want to build your own PXE server image, you have to make sure that that has UEFI bootloader.

### Usage

1. If you want to use your dnsmasq configuration, create a [`/opt/dnsmasq.conf`](http://oss.segetech.com/intra/srv/dnsmasq.conf) file on the Docker host

   ```ini
   #dnsmasq config, for a complete example, see:
   #  http://oss.segetech.com/intra/srv/dnsmasq.conf

   #log all dns queries
   log-queries

   #dont use hosts nameservers
   no-resolv

   # Enable DHCP logging
   log-dhcp

   # Disable DNS server
   port=0

   # Disable re-use of the DHCP servername and filename fields as extra
   # option space. That's to avoid confusing some old or broken DHCP clients.
   dhcp-no-override

   # Run as root user
   user=root

   # Respond to PXE requests for the specified network;
   # run as DHCP proxy
   # Change the IP address to your private LAN IP
   dhcp-range=192.168.30.2,192.168.30.254,255.255.255.0
   # Change the IP address to the PXE server
   dhcp-boot=tag:efi64,netboot.xyz.efi,,192.168.30.9

   dhcp-match=set:efi64,60,PXEClient:Arch:00007
   dhcp-match=set:efi64,60,PXEClient:Arch:00008
   dhcp-match=set:efi64,60,PXEClient:Arch:00009
   ```

2. Run the container

   ```bash
   docker run -d \
      --name dnsmasq \
      -v /opt/dnsmasq.conf:/etc/dnsmasq.conf \
      --log-opt "max-size=100m" \
      -e "HTTP_USER=foo" \
      -e "HTTP_PASS=bar" \
      --net host \
      --restart always \
      awesometic/dnsmasq
   ```

3. Visit `http://<docker-host>:8080`, authenticate with `foo/bar` and you should see

   <img width="833" alt="screen shot 2017-10-15 at 1 41 21 am" src="https://user-images.githubusercontent.com/633843/31580966-baacba62-b1a9-11e7-8439-ca1ddfe828dd.png">

#### MIT License

Copyright &copy; 2018 Jaime Pillora &lt;dev@jpillora.com&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[dockerhub]: https://hub.docker.com/r/jpillora/dnsmasq/
