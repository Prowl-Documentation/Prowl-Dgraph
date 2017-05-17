![documentations](http://getprowl.com/assets/images/documentation1.png)
<h1 align="center">Using Dgraph with Prowl & Docker Installation</h1>

If you're not familiar with Dgraph, visit their website at https://dgraph.io/. Look through their documentation, but in this instance, this documentation will be walking you through how to install what you need for this to run on the Prowl framework, and to get it running. 

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

Now update,  

<pre>sudo apt-get update</pre> 

Make sure Docker is installed via 

<pre>docker -v</pre>

Alternatively you can run 

<pre>sudo docker run hello-world</pre>

<h1 align="center">Dgraph Installation</h1>

Clear all the pre existing Docker information, now run 

<pre>curl https://get.dgraph.io > /tmp/get.sh
vim /tmp/get.sh  # Inspect the script
sh /tmp/get.sh   # Execute the script</pre>

In some cases vim /tmp/get.sh doesn't work, for some reason, so try 

<pre>vi /tmp/get.sh</pre> 

Assuming you have Docker installed and followed earlier instructions, pull Dgraph from Docker 

<pre>docker pull dgraph/dgraph</pre> 

Now you can actually run the dgraph command, by simply running

<pre>dgraph</pre> 

In this case, we are going to be binding dgraph on port 4040, run 

<pre>mkdir -p ~/dgraph
# Mapping port 4040 from within the container to 9090  of the instance
docker run -it -p 9090:4040 -v ~/dgraph:/dgraph dgraph/dgraph dgraph --bindall=true</pre>

Now via the information we get from the Prowlbox, we can search using Dgraph via mutation. An example of this, would be the following JSON contents

<pre>{
    "me": [
        {
            "artist": [
                {
                    "name": "Weiss"
                }
            ],
            "song_title": "Man Gone",
            "release_date": "2016-05-21",
            "running_time": 4:29,
                {
                    "label": "Toolroom"
                },
                {
                    "device_name": "JakesiPhone"
                },
                {
                    "playlist_boolean": "no"
                }
            ]
        },
        {
                }
            ]
        }
    ]
}</pre>

Some of the Docker engines have problems with Dgraph, so troubleshoot via 

<pre>docker run -it dgraph/dgraph bash</pre>
