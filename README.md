# What is the Plugin-Data-Pipeline (a.k.a PDP)

In order to test certain software via virtual nodes and observe how the data is parsed through the server and outputted to a web-interface, various plugins clients must be set up to process such data formulated by the software.
Thus, this documentation instructs you on how you can create and run your own your node-side and server-side plugins. Using this documentation, tutorials provided below and the existing virtual nodes, you can create node-side plugins that will test your software, through a publisher script, and implement server-side plugins, through a worker-client script, that can process and handle the incoming i/o from the node-side plugin.
The following sections below will explain on how to install the corresponding packages and dependencies, and will provide the steps to make your own publisher and worker scripts for software-analysis through Waggle's Beehive server.


# Installation

  * [PyWaggle](https://github.com/waggle-sensor/pywaggle.git)
  * [OpenSSL/TLS Certification & Registeration]()
  * [Hello Publisher](https://github.com/seanshahkarami/hello-publisher.git)
                     

# How to use PDP

VNE mainly comprises of two scripts, you the developer, must implement. The publisher script (node-side plugin) and the worker script (server-side plugin). Below is a diagram illustrating a model of how the end-to-end communication takes place between a publisher and a consumer/database.

|    Creating the Publisher Script:    |    Creating the Worker Script:    |
|:---------------|:--------------|
|The following module will explain how to implement the outershell of your plugin client.|The following module will explain how to implement the outershell of your worker script.|
|`#!/usr/bin/env python3`|`#!/usr/bin/env python3`|                
|`from waggle import beehive`| `from waggle import beehive` |
|`import pika`| `import pika` |
|The following packages and dependicies to import in order to run plugin. Other packages can be imported.|The following packages and dependicies to import in order to run worker client. Other packages can be imported.|
|`config = beehive.ClientConfig(`|`config = beehive.ClientConfig(`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`host='beehive.mcs.anl.gov',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`host='beehive.mcs.anl.gov',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`port=23181,`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`port=23181,`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`node='000002000000000(n)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`node='000002000000000(n)',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`username = '(username1)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`username = '(username2)',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`password = '(password1)'`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`password = '(password2)'`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cacert='/path/to/cacert.pem',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cacert='/path/to/cacert.pem',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cert='/path/to/cert.pem',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cert='/path/to/cert.pem',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`key='/path/to/key.pem')`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`key='/path/to/key.pem')`|
|The Config function initalizes your config paramters, allowing you to: Connect to beehive-dev's rabbitmq server, Connect and transmit data to the virtual node, and use certificates for authentiction purposes.|The Config function initalizes your config paramters, allowing you to: Connect to beehive-dev's rabbitmq server, Connect and transmit data to the virtual node, and uses certificates for authentication purposes.|
| | **Your code will be placed in this part of the script.** |
|`client = beehive.PluginClient(` | `def callback(data):`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`name='(name_of_queue)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`value = data.get('body')`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`config=config)`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return(value)`|
|This Client function allows you to create your client script and connection. It allows you to declare a queue to send all your information and sets your configuration parameters.|Receiving messages from the queue is more complex. It works by subscribing a callback function to a queue. Whenever you receive a message, this callback function is called by the Pika library. In this case the function will send the ouput to beehive-dev's interface: [http://10.10.10.5/?all=true](http://10.10.10.5/?all=true)
| **Your code will be placed in this part of the script.** |
|At the end of your program, in order to send your data/plugin to rabbitmq server, you must add the following line of code:|`client = beehive.PluginClient(`|
|`client.publish('(key)', (value))`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`name='(name_of_queue)',`|
|Sends it with a key and value.|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`config=config,`|
| |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`callback=callback)`|
| | This Client function allows you to create your client script and connection. It allows you to declare a queue to send all your information and sets your configuration parameters & callback function.|

# Outcome of PDP

The data sent by your node-side plugin is sent to db-raw at: http://10.10.10.5/?all=true

The processed data, done by server-side plugin, is sent to db-decoded to: http://10.10.10.5/?all=true

The following output data is in the form of a file that can be downloaded from the website. This website is beehive-dev's web interface.


# Sample Use of PDP

The following section will display a sample node-side plugin script and server-side plugin script used to send and process data through communication with beehive-dev.

The following code demonstrates a simple fibonacci program that runs within beehive-dev and computes the fibonacci number of any data, in the form of a number, sent through the plugin.

| Publisher Script: | Worker Script: |
|:------------------|:-----------------|
|![image 1](https://user-images.githubusercontent.com/25256730/28250983-a740e8b6-6a39-11e7-88b3-9a71f368088d.png)|![image](https://user-images.githubusercontent.com/25256730/28250974-96f004c4-6a39-11e7-96d9-470f9cf822b7.png)|

