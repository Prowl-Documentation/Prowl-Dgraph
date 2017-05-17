![documentations](http://getprowl.com/assets/images/documentation1.png)
<h1 align="center">Using dgraph with Prowl</h1>

If you're not familiar with dgraph, visit their website at https://dgraph.io/. Look through their documentation, but in this instance, this documentation will be walking you through how to install what you need for this to run on the Prowl framework, and to get it running. 

First access the Prowl VPS that all Prowl employees should haves access to. Make sure it's running the right kernel you can do this by running 

<pre>uname</pre>

I suggest you use these flags when running uname 

<pre>m, o</pre>

So the command should be

<pre>uname -mo</pre>

It obviously should be running the same kernel, since the VPS is the same all accross the board for each Prowl employee. Depending on what Prowl VPS you're on, you may need to update Docker, so run 

<pre>sudo apt-get remove docker docker-engine</pre>

The contents of /var/lib/docker/, including images, containers, volumes, and networks, are preserved. The Docker CE package is now called docker-ce, and the Docker EE package is now called docker-ee.

Now run to install Docker 

<pre>sudo apt-get update
sudo apt-get install \
linux-image-extra-$(uname -r) \
linux-image-extra-virtual</pre>

Now we are going to add the Docker repo, since all our VPS's are on 64-bit, you need to run 

<pre>sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
(lsb_release -cs) \
stable"</pre>
