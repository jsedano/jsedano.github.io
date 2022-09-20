---
layout: post
title:  "Trying out localstack with JUnit5"
date:   2020-12-22 00:00:00 -0600
categories: java
tags: java junit5 localstack
comments: true
---
I was trying to fake `Amazon Web Services` using `localstack` by running it with `Junit5` and this is what I did.

You can find the code for this example here: [localstack-example][localstack-example].

For this you will need docker. I installed `docker` on my mac with `brew` using this post as a guide: [How to install Docker on MacOS by Robin Wieruch][docker-macos] which basically consists on running the following:

{% highlight shell %}
brew update
brew install docker
brew install docker-machine
brew cask install virtualbox
{% endhighlight %}

That is all the stuff we need to install, then we need to create the  virtual machine with `virtualbox` which will be running `docker`.

{% highlight shell %}
docker-machine create --driver virtualbox default
{% endhighlight %}

And we start the virtual machine like this:

{% highlight shell %}
docker-machine start default
{% endhighlight %}

Now we need to run this:

{% highlight shell %}
docker-machine env default
{% endhighlight %}

Which will output something like this:

<pre>
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/jsedano/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell:
# eval $(docker-machine env)
</pre>

We then run that last sentence to prepare our shell with the environment variables needed:

{% highlight shell %}
eval $(docker-machine env)
{% endhighlight %}

That is it for the `docker` installation.

Now for `localstack` we will use [localstack-java-utils][localstack-java-utils] which already has a guide and some code we will use, but I had to add other stuff.

These are the dependencies needed:

{% highlight shell %}
  <!-- for easy amazon dependencies management -->
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-bom</artifactId>
        <version>1.11.923</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <!-- I only used s3 for this example -->
    <dependency>
      <groupId>com.amazonaws</groupId>
      <artifactId>aws-java-sdk-s3</artifactId>
    </dependency>

    <!-- I had to put this one since I think localstack-utils needs it -->
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-lang3</artifactId>
      <version>3.0</version>
    </dependency>

    <!-- JUnit5 -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.7.0-M1</version>
      <scope>test</scope>
    </dependency>

    <!-- localstack -->
    <dependency>
      <groupId>cloud.localstack</groupId>
      <artifactId>localstack-utils</artifactId>
      <version>0.2.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
{% endhighlight %}

And this is our test class:

{% highlight java %}
package dev.jsedano.example;

import cloud.localstack.awssdkv1.TestUtils;
import cloud.localstack.docker.LocalstackDockerExtension;
import cloud.localstack.docker.annotation.LocalstackDockerProperties;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.model.ObjectMetadata;
import com.amazonaws.services.s3.model.S3Object;
import com.amazonaws.services.s3.model.S3ObjectInputStream;
import com.amazonaws.util.IOUtils;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

import java.io.*;

import static org.junit.jupiter.api.Assertions.assertEquals;

@ExtendWith(LocalstackDockerExtension.class)
@LocalstackDockerProperties(services = {"s3"}, hostNameResolver = HostNameResolverImpl.class)
public class AppTest {

  @Test
  public void creatingBucketAndPuttingObject() throws IOException {
    String bucketName = "abucket";
    String fileName = "test.txt";
    String fileContent = "a String";

    AmazonS3 s3 = TestUtils.getClientS3();
    s3.createBucket(bucketName); //creating bucket

    InputStream inputStream = new ByteArrayInputStream(fileContent.getBytes());

    //uploading file to bucket
    s3.putObject(bucketName,fileName, inputStream, new ObjectMetadata());

    //downloading file from bucket
    S3Object s3Object = s3.getObject(bucketName, fileName);
    S3ObjectInputStream s3ObjectInputStream = s3Object.getObjectContent();

    //checking is the same as the info we uploaded
    assertEquals(fileContent, IOUtils.toString(s3ObjectInputStream));
  }

}
{% endhighlight %}

I was trying to run the test with `mvn clean verify` without setting up the `hostNameResolver` but it was failing with the following error:

<pre>
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 34.445 s <<< FAILURE! - in dev.jsedano.example.AppTest
[ERROR] creatingBucketAndPuttingObject  Time elapsed: 1.645 s  <<< ERROR!
com.amazonaws.SdkClientException: Unable to execute HTTP request: Connect to localhost.localstack.cloud:4566 [localhost.localstack.cloud/127.0.0.1] failed: Connection refused (Connection refused)
	at dev.jsedano.example.AppTest.creatingBucketAndPuttingObject(AppTest.java:29)
Caused by: org.apache.http.conn.HttpHostConnectException: Connect to localhost.localstack.cloud:4566 [localhost.localstack.cloud/127.0.0.1] failed: Connection refused (Connection refused)
	at dev.jsedano.example.AppTest.creatingBucketAndPuttingObject(AppTest.java:29)
Caused by: java.net.ConnectException: Connection refused (Connection refused)
	at dev.jsedano.example.AppTest.creatingBucketAndPuttingObject(AppTest.java:29)
</pre>

So I did this, which admittedly seems a little hackish...

{% highlight java %}
package dev.jsedano.example;

import cloud.localstack.docker.annotation.IHostNameResolver;

public class HostNameResolverImpl implements IHostNameResolver {
  @Override
  public String getHostName() {
      String dockerHost = System.getenv("DOCKER_HOST");
      return dockerHost.substring(dockerHost.lastIndexOf('/')+1, dockerHost.lastIndexOf(':'));
  }
}
{% endhighlight %}

If you remember the output of `docker-machine env default` there is an environment variable called `DOCKER_HOST` which contains the ip address of the underlying docker running on the virtual machine, and we only need the ip part, that's why we are using that substring.

There should be other ways to solve this, for example modifying the `hosts` file, or mapping the address to localhost with something like  `nginx`.

After that this is the output of running

{% highlight shell %}
mvn clean verify
{% endhighlight %}

<pre>
[INFO] Results:
[INFO]
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
</pre>

One of the things I couldn't do was to run this test using `IntelliJ`, there should be a way to set the environment variables (by telling IntelliJ to run `eval $(docker-machine env)`).

Download the complete code from this example here [localstack-example][localstack-example].

[localstack-example]: https://github.com/jsedano/examples/tree/main/localstack-example
[docker-macos]: https://www.robinwieruch.de/docker-macos
[localstack-java-utils]: https://github.com/localstack/localstack-java-utils
