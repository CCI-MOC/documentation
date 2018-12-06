The OpenStack Object Store project, known as Swift, offers cloud storage software so that you can store and retrieve lots of data with a simple API. It's built for scale and optimized for durability, availability, and concurrency across the entire data set. Swift is ideal for storing unstructured data that can grow without bound.

There are two apis viz. **pfioh and pman**, which interact with the Swift storage to get or put data.

For accessing the Swift storage, authorization credentials needs to provided as a secret, mounted into the pod. More information can be found at [Configuring Swift Credentials](https://github.com/awalkaradi95moc/pman/blob/master/openshift/README.rst) 

## pfioh

1) The data pushed to pfioh is stored in Swift. In Swift, an object container is created with the name as specified in the key. 

2) The container has two folders internally- input and output. As the name suggests, the **input data pushed to pfioh** is stored into the input folder.

3) To enable Swift Object store option for pfioh, start pfioh with --swift-storage option

    `pfioh --forever --httpResponse --swift-storage --createDirsAsNeeded`

## pman

1) pman needs to talk to Swift two times during the operation: **get** the data from Swift for image processor to perform some operation and **put** the output of image processor into the output folder of the data container. 

2) Initially, the input data is attached as a volume to the image processor using an **empty directory** mounted at /share **shared** within multiple containers of the image processor pod. 

3) The **init container** is pointed to pman-swift-publisher image which runs the script to **get** the data from Swift and put it into the /share folder.

4) Next, the image processor does some computation on the data and produces an output. This output is stored in the output folder of the data container.  

5) The key is again passed as an environment variable for the publish container of the image processor pod.

6) The **publish container** is pointed to pman-swift-publisher which runs the script to **put** the output from /share folder into Swift. 

7) However, the publish container has to know when the output has been generated. It polls the main OpenShift api to find out whether the job has been finished and then transfers the output into Swift.  

8) Once the publish container has finished its '**put**' operation, the job will finish.


