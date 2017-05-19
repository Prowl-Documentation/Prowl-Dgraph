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

Make sure you're connected to your VPS you're assigned to, we call this at Prowl the "VPS Cluster", this is all stated in our private repo of information, it should look something like this in the interface below. For the VPS clusters we use Docker Compose for Prowl it's great for development, testing, and staging environments, as well as CI workflows. You can learn more about each case in
[Common Use Cases](https://github.com/docker/docker.github.io/blob/master/compose/overview.md#common-use-cases).

| VPS   | Private IP address  (ethN) | VPN IP address (wg0) |
| ----- | -------------------------- | -------------------- |
| VPS1    | 10.8.23.93                 | 10.0.1.1         |
| VPS2    | 10.8.23.94                 | 10.0.1.2         |
| Docker  | 10.8.23.95                 | 10.0.1.3         |
| Docker2 | 10.8.23.95                 | 10.0.1.3         |

The Docker client will honor the DOCKER_HOST environment variable to set the -H flag for the client. The unknown directive is treated as a comment due to not being recognized. In addition, the known directive is treated as a comment due to appearing after a comment which is not a parser directive

<pre>unknowndirective=value
knowndirective=value</pre>

<h1 align="center">Installing Docker using DigitalOcean</h1>

Prowl also uses DigitalOcean in some instances for VPS's, we have our own VPS's but sometimes we use DigitalOcean. So let's get started installing Docker on DigitalOcean. Let's create a user 

<pre>sudo useradd -m -d /home/PROWL -s /bin/bash -U PROWL</pre> 

Now get a password

<pre>passwd USER</pre>

Since all Prowl employees have access to the DigitalOcean account, we will use groupadd

<pre>groupadd admin && usermod -a -G admin PROWL</pre>

Login with

<pre>su PROWL</pre>

Go to the home directory in the VPS 

<pre>cd ~/</pre>

Now we need to update, and install dependencies

<pre>sudo apt-get update
sudo apt-get install linux-image-extra-`uname -r`</pre>

You'll be prompted with a screen select the following 

<pre>keep the local version currently installed</pre>

Now we need to install Go, which is what Docker is written in 

<pre>wget http://go.googlecode.com/files/go1.1.1.linux-amd64.tar.gz
tar xf go1.1.1.linux-amd64.tar.gz
rm go1.1.1.linux-amd64.tar.gz</pre>

Now we are going to add some environment variables

<pre>echo "export GOROOT=\$HOME/go" >> ~/.profile
echo "PATH=$PATH:\$GOROOT/bin" >> ~/.profile
source ~/.profile</pre>

Now it's time to actually install Docker 

<pre>sudo apt-get install lxc curl xz-utils git mercurial</pre>

Create this folder

<pre>mkdir -p $GOPATH/src/github.com/dotcloud</pre>

You'll want to change directories and clone the Docker image from GitHub

<pre>cd $GOPATH/src/github.com/dotcloud
git clone https://github.com/dotcloud/docker.git</pre>

Now let's symlink Docker

<pre>sudo ln -s $GOPATH/bin/docker /usr/local/bin/docker</pre>

Let's run Docker

<pre>sudo docker -d &</pre>

Once you've made sure Docker is installed correctly, let's pull some base files

<pre>docker pull base</pre>

Now, let's open a Prowl test container to test if everything is working correctly

<pre>docker run -t base /bin/echo "Hello, world."</pre>

There you go, you now have installed Docker using a DigitalOcean VPS that we use.

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

Then try running Dgraph again

<pre>dgraph</pre>

<h1 align="center">How it should look</h1>

This is how it should look if you followed the instructions correctly 

<img src="https://www.getprowl.com/assets/images/dgraph.png" alt="dgraph" width="651" height="498">

<h1 align="center">Hooks</h1>

Model hooks are available on both serialization and deserialization. These can be used to control what of the model data gets serialized (eg. sent to the client) or what will gets written into the model after deserialization.

Here are the available callbacks (can be directly implemented on the `DataObject` or in a `DataExtension`)

Signature | Parameter type | Info 
--- | :---: | ---
`onBeforeDeserialize(&$data)` | `string` | Called before the raw JSON is being parsed. You get access to the raw JSON data sent by the client.
`onAfterDeserialize(&$data)` | `array` | Called after JSON has been deserialized into an array map. You can modify this array to prevent incoming values to be applied to your model (sanitize incoming data).

This of course is to parse the JSON information you are getting from Dgraph, this is a feature within Prowl. In conclusion, Dgraph is a quick way to search for information Prowl collects, it wouldn't be our main way to search for data, but for on the go situations, this is very ideal. 

